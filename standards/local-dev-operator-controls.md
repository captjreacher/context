# Local Dev Operator Controls Standard

## Purpose

Local development tools may expose operator controls for starting, stopping, and checking the status of local ecosystem services when those controls improve developer workflow and remain confined to a developer workstation.

These controls are developer workstation tooling. They are not hosted orchestration, production operations, or a substitute for a real control plane.

## Scope

This standard applies to any ecosystem product or agent that adds local UI controls for service lifecycle operations, including controls exposed through development server middleware, dev-only panels, local dashboards, or similar tooling.

Implementation-local details belong in the owning product repository. This standard defines only the reusable governance rules.

## Development-only rule

Local operator controls may be registered only in development server mode.

They must never ship in static output, production builds, hosted preview output, or production runtime code. Production control planes require proper backend APIs, authentication, authorization, environment separation, and audit logging.

## Allowed use

Local dev UIs may expose start, stop, restart, health, and status controls when all of the following are true:

1. The target service is intended to run on the same developer workstation.
2. The control surface is available only from the local development server.
3. The action maps to a fixed allowlisted operation owned by the service or repository.
4. The UI and middleware return readable JSON success and error responses.
5. Failure states are visible enough for a developer to understand what happened without exposing secrets.

## Required controls

Any local operator control endpoint must:

1. Register only in development server mode.
2. Never ship in static or production output.
3. Reject non-localhost socket, `Host`, and `Origin` values.
4. Use fixed allowlisted commands only.
5. Never accept arbitrary commands, script names, paths, or shell arguments from the client.
6. Return readable JSON errors.
7. Prefer service-owned scripts rather than duplicating lifecycle logic in the UI or middleware.
8. Treat local operator controls as developer workstation tooling, not hosted orchestration.

## Command handling

The client may request only named actions such as `start`, `stop`, or `status`. The server-side development middleware must map those names to fixed allowlisted commands.

The client must not provide:

- executable names
- package script names
- filesystem paths
- command arguments
- shell fragments
- environment variable overrides

If an action is not recognized, the endpoint must reject it with a clear JSON error.

## Network boundary

Local operator controls must validate the request boundary before running any lifecycle action.

At minimum, reject requests when:

- the socket is not local
- the `Host` header is not localhost or an approved loopback host
- the `Origin` header is present and not localhost or an approved loopback origin

These checks protect development-only endpoints from being accidentally exposed through tunnels, proxies, hostile pages, or misconfigured hosts.

## Production boundary

Do not extend local operator controls into production by adding exceptions, feature flags, or hosted shortcuts.

A production-grade control plane must be designed separately and must include:

- backend APIs with explicit contracts
- authentication
- authorization
- environment separation
- audit logging
- operational monitoring and alerting
- secret-safe execution

Local controls may inform that design, but they must not become it by gradual expansion.
