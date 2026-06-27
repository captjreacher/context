# Supabase Data API Exposure Standard

All migrations that create tables in `public` must explicitly decide Data API exposure.

Supabase is changing the default behavior for new `public.*` tables so they are no longer exposed to the Data API unless explicit grants are included. This affects access through PostgREST, `supabase-js`, GraphQL, and any other client path that depends on the Supabase Data API.

## Rollout context

- May 30, 2026: applies to new Supabase projects.
- October 30, 2026: applies to existing Supabase projects.

Agents must write migrations as if explicit grants are required now.

## Core rule

Every migration that creates a `public.*` table must include an explicit API exposure decision:

1. Which roles receive table grants.
2. Whether row level security is enabled.
3. Which RLS policies are required for exposed roles.
4. Whether anonymous access is intentionally allowed.

`GRANT` controls API and table exposure. RLS controls row-level access after a role can access the table. `GRANT` is not a replacement for RLS, and RLS is not a replacement for grants.

Missing grants can cause PostgREST, `supabase-js`, GraphQL, or other Data API access failures even when the table and RLS policies otherwise look correct.

## Required default

Every new `public.*` table must include:

1. Explicit `GRANT` statements
2. Explicit RLS enablement or a documented reason not to enable RLS
3. Policies where user-owned or public-readable data is exposed
4. No anonymous access unless intentionally justified
5. No reliance on Supabase default grants

## Exposure tiers

| Tier | Example | Data API grants | RLS |
|---|---|---|---|
| Internal system tables | raw operational tables, event tables, analytics tables, sync state, workflow queues | `service_role`; `authenticated` only when required by application code | required unless accessed only through trusted backend/service role |
| User-owned app data | profiles, deployments, narratives, user documents | `authenticated`, `service_role` | required |
| Public-read projections/views | public profile projection, public deployment projection, public catalog view | `anon select` only when intentionally public; `service_role` | strict public-read policy |

Raw operational tables, event tables, analytics tables, workflow queues, and user-owned tables must not receive `anon` grants by default.

Public anonymous access should preferably go through Edge Functions or dedicated public projections/views rather than direct access to raw tables.

## Grant and RLS guidance

Use grants to decide which API roles can reach the table at all:

- `service_role` for trusted backend and administrative operations.
- `authenticated` for signed-in user application access.
- `anon` only for intentionally public read paths.

Use RLS policies to enforce which rows each exposed role can read or mutate.

For user-owned app data, enabling RLS is required before granting access to `authenticated`. Policies must bind access to the authenticated user or another explicit authorization model.

For public-read projections or views, prefer exposing only the fields intended for public consumption. Do not expose raw operational or user-owned tables anonymously just because a UI needs a small public subset.

## Default migration pattern

```sql
create table public.example (
  id uuid primary key default gen_random_uuid(),
  created_at timestamptz not null default now()
);

alter table public.example enable row level security;

grant select, insert, update, delete
on table public.example
to authenticated;

grant all
on table public.example
to service_role;
```

This pattern intentionally does not grant `anon`. Add anonymous grants only for a deliberate public-read projection or view with matching RLS policy.
