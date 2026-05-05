# One-Shot App Staging Standard

## Purpose

This standard defines how temporary, experimental, AI-built, or client-preview applications are deployed into the MGRNZ ecosystem without accidentally becoming production systems.

It exists to keep 1-shot apps fast while preserving central control, reviewability, and future Cockpit visibility.

## Canonical staging target

All 1-shot app static builds deploy to:

```txt
Repository: captjreacher/prod-staging
Domain: https://staging.maximisedai.com
```

Each app must deploy to its own subfolder:

```txt
https://staging.maximisedai.com/<app-name>/
```

Examples:

```txt
https://staging.maximisedai.com/instamenu/
https://staging.maximisedai.com/petfilth-preview/
https://staging.maximisedai.com/client-demo-alpha/
```

## Operating model alignment

This standard supports the deployment state model:

```txt
Local Dev → Ready to Push → GitHub Action Running → Staging → Approved → Production → Failed
```

In the current phase, GitHub Actions and GitHub Pages perform the deployment. In a later phase, deploy events should be emitted into the event framework and surfaced in Cockpit.

## Repo responsibilities

| Repo | Responsibility |
|---|---|
| Source app repo | Source code, dependencies, framework config, tests, build command, deploy workflow. |
| `captjreacher/prod-staging` | Static staging output, custom staging domain, committed deploy artifacts. |
| Future Cockpit/Paperclip | Deployment visibility, approval state, promotion control, failure escalation. |

## Rules

1. A 1-shot app must not deploy directly to a production domain.
2. A 1-shot app must deploy to a unique subfolder under `staging.maximisedai.com`.
3. Source repos must build locally and in GitHub Actions before pushing artifacts to staging.
4. Framework base paths must match the staging subfolder.
5. Secrets must never be committed.
6. Service-role keys must never be used in frontend builds.
7. Promotion from staging to production requires explicit approval.
8. Staging apps should be disposable unless promoted into a product repo.

## Required GitHub secret

Each source repo that deploys to central staging needs:

```txt
STAGING_DEPLOY_TOKEN
```

The token must allow write access to `captjreacher/prod-staging`.

Use least privilege where possible.

## Standard environment variables for workflows

```yaml
env:
  STAGING_REPO: captjreacher/prod-staging
  STAGING_BRANCH: main
  STAGING_PATH: your-app-name
  BUILD_COMMAND: npm run build
  BUILD_OUTPUT: dist
```

## Common build types

### Vite / React / vanilla Vite

Build:

```bash
npm ci
npm run build
```

Output:

```txt
dist
```

Vite base path must match the staging path:

```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  base: '/your-app-name/',
})
```

### Static HTML/CSS/JS

Build:

```bash
mkdir -p dist
cp -R index.html assets dist/
```

Output:

```txt
dist
```

Use relative links where possible:

```html
<link rel="stylesheet" href="./assets/style.css">
<script src="./assets/app.js"></script>
```

### Next.js static export

Only use GitHub Pages staging for static exportable Next.js apps.

Not suitable:

- runtime API routes
- SSR-only pages
- server actions
- backend-dependent logic without external hosting

Recommended `next.config.js`:

```js
const stagingPath = process.env.STAGING_PATH || ''

const nextConfig = {
  output: 'export',
  trailingSlash: true,
  basePath: stagingPath ? `/${stagingPath}` : '',
  assetPrefix: stagingPath ? `/${stagingPath}/` : '',
  images: {
    unoptimized: true,
  },
}

module.exports = nextConfig
```

Build:

```bash
STAGING_PATH=your-app-name npm run build
```

Output:

```txt
out
```

### Astro

```js
import { defineConfig } from 'astro/config'

export default defineConfig({
  site: 'https://staging.maximisedai.com',
  base: '/your-app-name',
})
```

Output:

```txt
dist
```

### SvelteKit static adapter

Use `@sveltejs/adapter-static`.

```js
import adapter from '@sveltejs/adapter-static'

const config = {
  kit: {
    adapter: adapter({
      pages: 'build',
      assets: 'build',
      fallback: 'index.html',
      precompress: false,
      strict: false,
    }),
    paths: {
      base: '/your-app-name',
    },
  },
}

export default config
```

Output:

```txt
build
```

## Template source repo workflow

Create this file in the source app repo:

```txt
.github/workflows/deploy-to-staging.yml
```

```yaml
name: Deploy to central staging

on:
  push:
    branches:
      - main
  workflow_dispatch:

env:
  STAGING_REPO: captjreacher/prod-staging
  STAGING_BRANCH: main
  STAGING_PATH: your-app-name
  BUILD_COMMAND: npm run build
  BUILD_OUTPUT: dist

permissions:
  contents: read

concurrency:
  group: staging-${{ github.repository }}
  cancel-in-progress: true

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout source repo
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Build source app
        run: ${{ env.BUILD_COMMAND }}

      - name: Validate build output
        run: |
          test -d "${{ env.BUILD_OUTPUT }}"
          test -f "${{ env.BUILD_OUTPUT }}/index.html"

      - name: Checkout staging repo
        uses: actions/checkout@v4
        with:
          repository: ${{ env.STAGING_REPO }}
          ref: ${{ env.STAGING_BRANCH }}
          token: ${{ secrets.STAGING_DEPLOY_TOKEN }}
          path: staging

      - name: Copy build into staging path
        run: |
          rm -rf "staging/${{ env.STAGING_PATH }}"
          mkdir -p "staging/${{ env.STAGING_PATH }}"
          cp -R "${{ env.BUILD_OUTPUT }}/." "staging/${{ env.STAGING_PATH }}/"
          touch staging/.nojekyll

      - name: Commit and push staging update
        working-directory: staging
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add "${{ env.STAGING_PATH }}" .nojekyll
          git commit -m "Deploy ${{ github.repository }} to staging/${{ env.STAGING_PATH }}" || echo "No changes to deploy"
          git push

      - name: Print staging URL
        run: echo "https://staging.maximisedai.com/${{ env.STAGING_PATH }}/"
```

## Standard coding-agent instruction

Use this instruction when asking an agent to create or deploy a 1-shot app:

```md
Read the shared context repo first: https://github.com/captjreacher/context
Follow `standards/one-shot-app-staging.md`.
Deploy target is central staging only.
Do not create a production deployment.
Use `captjreacher/prod-staging` as the staging output repo.
Deploy built static output to `/<repo-name>/`.
The public staging URL must be `https://staging.maximisedai.com/<repo-name>/`.
For Vite, set `base: '/<repo-name>/'`.
Add `.github/workflows/deploy-to-staging.yml` using the central staging template.
Assume `STAGING_DEPLOY_TOKEN` exists as a GitHub Actions secret.
Never commit `.env`, private keys, or service-role keys.
```

## Event framework future hook

Later, each deployment should emit an event such as:

```json
{
  "event_type": "deployment.staging.published",
  "source_system": "github_actions",
  "entity_type": "deployment",
  "entity_ref": "repo-name/staging-path",
  "status": "published",
  "payload": {
    "source_repo": "captjreacher/example-app",
    "staging_repo": "captjreacher/prod-staging",
    "staging_url": "https://staging.maximisedai.com/example-app/",
    "commit_sha": "<sha>",
    "workflow_run_id": "<run_id>"
  }
}
```

This is not required for the current GitHub Pages baseline, but agents should not design against it.
