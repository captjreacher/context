# CV Analytics Events V1

This file defines the event taxonomy for CV analytics tracking in the MGRNZ ecosystem.
The producing system is **PlayerCard** (the CV deployment and personalisation product).
Events flow through MGRNZ and are consumed by analytics aggregators, the user dashboard,
and downstream business intelligence tooling.

The central question these events answer:
**"Which version of my CV is getting attention, and how is that attention being driven?"**

---

## Event Taxonomy

Events follow dot-notation hierarchy: `cv.<domain>.<action>`

| Event Name | Domain | Description |
|---|---|---|
| `cv.deployment.created` | deployment | A CV version is published and a shareable deployment URL is issued |
| `cv.link.viewed` | link | A shared CV link is accessed by a viewer |
| `cv.link.shared` | link | The user deliberately shares a CV link (copy, send, export) |
| `cv.version.archived` | version | The user archives a CV version, removing it from active use |
| `cv.analytics.viewed` | analytics | The user opens the analytics dashboard for any CV version |
| `cv.archetype.selected` | archetype | The user selects or changes a CV archetype (e.g. Strategic Leader, Operator) |

Events are the source of truth. Derived UI state (dashboards, version badges, archetype labels)
is computed from the latest events, not stored independently.

---

## Event Definitions

### `cv.deployment.created`

**Trigger**: Fires when a user publishes a CV version and a stable deployment URL is issued.

**Required fields**

- `user_id` (UUID) — authenticated user who owns the CV
- `profile_id` (UUID) — the CV profile being deployed
- `version_id` (UUID) — unique identifier for this specific version snapshot
- `deployment_url` (URL) — the public URL issued for this deployment

**Optional fields**

- `archetype_type` (enum) — archetype active at time of deployment
- `version_label` (string) — human-readable label, e.g. "v3 – Senior IC"
- `timestamp` (ISO 8601) — defaults to server receipt time if omitted

**Consumers**: Analytics aggregator, User dashboard, Business intelligence

---

### `cv.link.viewed`

**Trigger**: Fires when a shared CV deployment URL is accessed by any viewer. Passive event — viewer authentication not required.

**Required fields**

- `profile_id` (UUID) — the CV profile the link belongs to
- `version_id` (UUID) — the specific version being viewed
- `deployment_url` (URL) — the URL that was accessed

**Optional fields**

- `user_id` (UUID) — present only when the viewer is the CV owner
- `traffic_source` (enum) — `direct`, `social`, `email`, `unknown`
- `referrer_domain` (string) — domain portion of HTTP referrer only (e.g. `linkedin.com`)
- `timestamp` (ISO 8601)

**Consumers**: Analytics aggregator, User dashboard, Business intelligence

**Privacy**: No full referrer URL stored. No viewer identity, IP, or device info captured.

---

### `cv.link.shared`

**Trigger**: Fires when the CV owner takes a deliberate share action (copy link, send, export).

**Required fields**

- `user_id` (UUID) — the CV owner performing the share
- `profile_id` (UUID) — the CV profile being shared
- `version_id` (UUID) — the specific version being shared
- `deployment_url` (URL) — the URL being shared

**Optional fields**

- `share_method` (enum) — `copy_link`, `email`, `social`, `export`, `other`
- `archetype_type` (enum) — archetype active at time of sharing
- `timestamp` (ISO 8601)

**Consumers**: Analytics aggregator, User dashboard, Business intelligence

**Privacy**: No recipient information captured. No third-party platform identifiers stored.

---

### `cv.version.archived`

**Trigger**: Fires when the user explicitly archives a CV version.

**Required fields**

- `user_id` (UUID) — the CV owner
- `profile_id` (UUID) — the CV profile containing the version
- `version_id` (UUID) — the version being archived

**Optional fields**

- `archetype_type` (enum) — archetype on the archived version
- `reason` (string) — optional short string (max 100 chars)
- `timestamp` (ISO 8601)

**Consumers**: Analytics aggregator, User dashboard, Business intelligence

---

### `cv.analytics.viewed`

**Trigger**: Fires when the CV owner opens the analytics dashboard.

**Required fields**

- `user_id` (UUID) — the authenticated CV owner
- `profile_id` (UUID) — the CV profile whose analytics are being viewed

**Optional fields**

- `version_id` (UUID) — if scoped to a specific version
- `timestamp` (ISO 8601)

**Consumers**: Analytics aggregator, Business intelligence

---

### `cv.archetype.selected`

**Trigger**: Fires when the user selects or changes the archetype for a CV version.

**Required fields**

- `user_id` (UUID) — the CV owner
- `profile_id` (UUID) — the CV profile being configured
- `archetype_type` (enum) — the archetype selected (e.g. `strategic_leader`, `operator`, `builder`, `specialist`, `commercial_driver`, `transformer`)

**Optional fields**

- `version_id` (UUID) — the version the archetype is being applied to
- `previous_archetype_type` (enum) — archetype before this change
- `timestamp` (ISO 8601)

**Consumers**: Analytics aggregator, User dashboard, Business intelligence

---

## Privacy Constraints (System-Wide)

The following data is explicitly **not** captured by any event:

- IP addresses of any party (owner or viewer)
- Device identifiers, user-agent strings, or browser fingerprints
- Full referrer URLs (path, query string, or fragment)
- Viewer identity when accessing a public CV link
- Geographic location derived from network data
- Session duration, scroll behaviour, or interaction-level telemetry

---

## Version History

| Version | Date | Author | Changes |
|---|---|---|---|
| v1 | 2026-05-07 | captjreacher | Initial draft — six core CV analytics events defined |
