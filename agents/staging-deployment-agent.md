# Staging Deployment Agent Guide

## Purpose

Prepare new repositories for deployment to the MAXAI staging environment.

## Steps

1. Read this context repository.
2. Confirm the repo is a new build or one-shot app.
3. Create or confirm an app_slug.
4. Add a deployment manifest using templates/deployment-manifest.example.json.
5. Add a GitHub Actions workflow using the staging template.
6. Ensure deployment target is staging.maximisedai.com.

## Rules

- Deployments must be staging only.
- Do not create production deployment workflows.
- Do not modify existing production repos.

## Output

A repo that builds and deploys to staging using a consistent pattern.
