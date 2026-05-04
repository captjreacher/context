# Base Path Enforcement

## Purpose

Apps deployed to MAXAI staging are served under a path:

```text
https://staging.maximisedai.com/<app-slug>/
```

Therefore frontend builds must generate asset URLs that work from `/<app-slug>/`, not from `/`.

## Rule

Every new staged frontend app must configure its public base path to match the app slug.

## Vite

Use one of these patterns.

### Environment-driven base path

```ts
import { defineConfig } from 'vite'

const appSlug = process.env.APP_SLUG || ''

export default defineConfig({
  base: appSlug ? `/${appSlug}/` : '/',
})
```

### Hard-coded app base path

```ts
import { defineConfig } from 'vite'

export default defineConfig({
  base: '/one-shot-app/',
})
```

The environment-driven option is preferred for reusable templates.

## Validation

After build, check that generated HTML references assets using the app slug path.

Expected pattern:

```text
/<app-slug>/assets/
```

Bad pattern:

```text
/assets/
```

## Build-Time Check

A staging workflow should fail if `dist/index.html` contains root asset paths such as:

```text
src="/assets/
href="/assets/
```

unless the app has an explicit exception documented in its deployment manifest.

## Agent Requirement

Before adding a staging deploy workflow, agents must inspect frontend config and ensure the app supports path-based hosting.
