# Staging Implementation — MAXAI

## Purpose

This document explains how new builds and one-shot apps are served on MAXAI staging.

## DNS Strategy

Use one DNS record for the staging host:

```text
staging.maximisedai.com
```

Do not create DNS records for individual apps or demo pages.

Each app is served as a path under the staging host:

```text
https://staging.maximisedai.com/<app-slug>/
```

Examples:

```text
https://staging.maximisedai.com/one-shot-app/
https://staging.maximisedai.com/risk-map-demo/
https://staging.maximisedai.com/client-preview/
```

## Deployment Mapping

Each repo defines:

- `APP_SLUG`
- `BUILD_OUTPUT_DIR`, default `dist`

The build output is uploaded to the staging host under the app slug:

```text
<build-output-dir>/ → <staging-root>/<app-slug>/
```

Example:

```text
dist/index.html
→ <staging-root>/one-shot-app/index.html
→ https://staging.maximisedai.com/one-shot-app/
```

## cPanel Path Options

The exact server path depends on how cPanel maps the subdomain.

Common option A:

```text
public_html/staging/<app-slug>/
```

Common option B:

```text
staging.maximisedai.com/<app-slug>/
```

Agents must not guess the final server path. Use the configured staging root for the repo or deployment environment.

## Repo Structure

For a typical Vite or static frontend repo:

```text
repo/
├─ index.html
├─ src/
├─ public/
├─ package.json
└─ dist/
   └─ index.html
```

The source `index.html` normally lives at the repo root.

The deployed `index.html` is the generated build output:

```text
dist/index.html
```

Only the build output directory is deployed.

## Frontend Base Path

Apps must support subpath hosting.

For Vite apps, configure the base path to match the app slug:

```ts
export default defineConfig({
  base: '/<app-slug>/',
})
```

Without this, JavaScript and CSS assets may try to load from the staging root instead of the app path.

Bad asset path example:

```text
https://staging.maximisedai.com/assets/app.js
```

Correct asset path example:

```text
https://staging.maximisedai.com/one-shot-app/assets/app.js
```

## Rules

- Leave DNS alone after the staging host is configured.
- Do not create per-app subdomains.
- Do not deploy apps to staging root `/`.
- Always deploy apps under `/<app-slug>/`.
- Do not create production workflows for new builds.
- Treat Cockpit bucket state as derived from deployment events.

## Agent Checklist

Before adding a staging workflow, confirm:

- the app has a safe slug
- the app builds locally
- the app has a known build output directory
- the frontend base path supports `/<app-slug>/`
- deployment target is under `staging.maximisedai.com/<app-slug>/`
