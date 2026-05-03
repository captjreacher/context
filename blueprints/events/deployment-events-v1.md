# Deployment Events V1

This file defines deployment event names for Cockpit tracking.

## Core Events

- deployment.requested
- deployment.created
- deployment.git.push_detected
- deployment.git.workflow_started
- deployment.git.workflow_completed
- deployment.git.workflow_failed
- deployment.build.started
- deployment.build.completed
- deployment.build.failed
- deployment.staging.started
- deployment.staging.completed
- deployment.staging.failed
- deployment.staging.url_issued
- deployment.approval.requested
- deployment.approval.granted
- deployment.approval.rejected
- deployment.failed
- deployment.retry_requested
- deployment.retry_completed

## Future Placeholders

- deployment.production.requested
- deployment.production.completed
- deployment.production.failed
- deployment.security.scan_started
- deployment.security.scan_failed
- deployment.performance.tested
- deployment.performance.failed
- deployment.audit.logged
- deployment.notification.sent
- deployment.domain.attached
- deployment.domain.failed

## Required Payload Fields

- project
- app_slug
- repo
- environment
- url
- commit_sha
- workflow_run_id

## Cockpit Buckets

- Local Dev
- Ready to Push
- GitHub Action Running
- Staging
- Approved
- Production
- Failed

Cockpit buckets are derived from latest deployment events. Events are the source of truth.
