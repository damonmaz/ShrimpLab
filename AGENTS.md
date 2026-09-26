<!-- BEGIN:nextjs-agent-rules -->

# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` (resolved from this file's directory; in monorepos the `next` package may not be visible from the repo root) before writing any code. Heed deprecation notices.

This block is written and re-added by `next dev` — verify at `node_modules/next/dist/server/lib/generate-agent-files.js`. Removing it from a diff only re-creates the uncommitted change; committing it with your work keeps the tree clean.

<!-- END:nextjs-agent-rules -->

# AGENTS

## Mission
This repo is a modular, portable, local-first homelab dashboard for monitoring servers, Docker workloads, and service health. Treat self-hosted operation as the default; optimize for reliability, observability, and portability across laptops, mini PCs, VMs, and Raspberry Pi hosts.

## Core rules
- Keep the app modular and composable. Split UI, domain logic, and integration code into clear layers.
- Prefer local-first data sources: localhost, LAN, Docker APIs, SSH, local REST endpoints, and host metrics. Avoid cloud dependencies unless explicitly required.
- Place all external integrations behind adapters. The UI must not call Docker, system, or service APIs directly.
- Define typed adapter contracts and normalize raw responses into shared domain models before rendering.
- Fail gracefully: missing data, stale metrics, and failed checks should degrade to a safe status instead of crashing the app.
- Never commit secrets, tokens, Docker socket paths, SSH keys, or production hostnames. Use env vars, example files, or local-only config only.
- Keep the dashboard portable: avoid hardcoded assumptions about a single platform, host, or deployment model.
- Prefer explicit status states such as healthy, degraded, down, and unknown over vague or misleading values.

## App structure
- app/: route-level pages and top-level composition only.
- components/: reusable components, such as presentational widgets and dashboard cards.
- features/: feature-specific pages, hooks, adapter and component integration and screen composition.
- adapters/: API, MQTT, Docker, host, service, and external integration implementations.
- lib/: shared utilities, formatters, polling helpers, and generic logic.
- config/: stores yaml configurations for servers, docker containers, etc. Allows users to modify what is monitored
- Keep new code near the relevant feature or integration; do not mix transport logic into presentation code.

## App flow
- adapters fetch Docker / API / MQTT data
- services combine and normalize adapters and libs
- components render it
- app pages call the services

## Adapter boundaries
- Adapters are the only layer that knows how to talk to Docker, system metrics, service checks, or remote endpoints.
- Convert external payloads into stable internal types with consistent naming and error handling.
- Keep retry, timeout, and fallback logic inside adapters, not inside components.
- If a source is unavailable, surface a clear degraded or unknown state rather than raising an uncaught exception.

## Server and Docker monitoring expectations
- Support server health views for CPU, memory, disk, load, network, and uptime.
- Support Docker monitoring for containers, images, status, restarts, and health trends.
- Keep metrics collection lightweight and safe for local resource-constrained hosts.
- Highlight stale or partial data clearly; freshness is part of the health model.
- Preserve a simple default experience for self-hosted deployments without requiring a cloud dashboard.

## Local deployment safeguards
- Prefer local Docker Compose, VM, or single-host execution flows over remote or hosted deployment paths.
- Keep startup and config steps simple, reproducible, and documentation-friendly.
- Validate with local lint/build checks before promoting a change to deployment configuration.
- Do not introduce public ingress, remote telemetry, or external credentials by default.
- Gate privileged features behind explicit configuration and document their risk clearly.
- If a feature depends on the Docker socket or host internals, make that dependency obvious and optional when possible.

## Implementation guidance
- Use TypeScript with clear interfaces and minimal implicit any.
- Prefer async, typed data flows with consistent loading, error, and empty states.
- Keep components deterministic and presentation-focused; push orchestration into services or adapters.
- Maintain readable code and avoid hidden cross-coupling or global state where a local module is sufficient.
- Optimize for the operator experience: fast status checks, clear health indicators, and minimal setup friction.

## Product direction and MVP scope
- Define the core domain model:
  - Server
  - Container
  - Health status
  - Alert or issue
  - Time-based snapshot
- Keep the data flow explicit:
  - upstream source such as Docker, local API, config, or mock data
  - adapter layer
  - normalized model
  - dashboard UI
- Maintain portability by keeping the UI generic and avoiding hard-coded hostnames, ports, or vendor assumptions.
- The first version should include:
  - summary cards for overall health
  - server panels with status, uptime, and resource data
  - container list with state, image, health, restart status, and ports
  - status states such as healthy, degraded, offline, and unknown
- Keep the dashboard portable:
  - use config-driven server definitions instead of hard-coded names
  - keep Docker integration behind adapter code
  - use a normalized data contract so mock data, JSON files, and live APIs all render the same
  - avoid cloud assumptions or external auth requirements


This repo is for a self-hosted operations dashboard, not a public SaaS product. Keep it local, portable, observable, and safe by default.