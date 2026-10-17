> ⚠️ **Personal work in progress — NOT an official OpenChamber extension.**
> This is a personal, public project shared openly (MIT, like other extensions). It is not submitted to any official catalog. Feedback and improvements are welcome — use at your own risk until the author approves it for general use.

# Containers extension for OpenChamber

A comprehensive OpenChamber GUI panel to inspect and manage **Docker or Podman** containers, Compose stacks, images, volumes, networks, and system storage.

It shells out to the `docker` or `podman` binary on PATH using host execution permissions (`permissions.exec`). The active runtime is auto-detected at startup (Podman first, then Docker) and can be switched at any time from the panel header.

> **Credit** — This extension is a dual-runtime port of
> [`openchamber/openchamber-extension-docker`](https://github.com/openchamber/openchamber-extension-docker),
> created by the OpenChamber project. The original panel, service, views, and MIT license are reused here.

## Runtimes

| Runtime | Autodetection | Override |
| --- | --- | --- |
| Podman | `podman info` answers first | `OPENCHAMBER_CONTAINER_RUNTIME=podman` |
| Docker | fallback when Podman is unavailable | `OPENCHAMBER_CONTAINER_RUNTIME=docker` |

The agent exposes `GET /runtime` (current runtime) and `POST /runtime` with `{ "runtime": "docker" | "podman" }`. Switching validates the allowlist and probes the binary with `<runtime> info`; a non-responding binary returns `502`.

Command output is normalized per runtime (Podman 4.9.3 verified empirically):

- **ps** — `Id`/`ID`, `Names` (array joined to a comma-separated string), and `Status` derived from `State` (`running` → `Up`).
- **stats** — Podman reports `ContainerID` plus raw byte counts (`MemUsage`, `MemLimit`) and numeric ratios (`CPU`, `MemPerc`); Docker reports preformatted strings.
- **images** — `repository`/`Repository`, `Id`/`ID`, `Size` (bytes normalized to MiB/KiB), and `Created` epoch converted to ISO 8601.
- **networks** — `id`/`ID`, `name`/`Name`, `driver`/`Driver`, `scope`/`Scope`.

## Features

### Containers & Compose
- **Container Lifecycle Management**: Start, stop, restart, pause, unpause, and force-remove containers.
- **Compose Project Grouping**: Automatic visual grouping of containers by Compose project with project-level batch controls.
- **Live Resource Usage**: Compact real-time CPU and RAM metrics for running containers.
- **Port Forwarding**: One-click opening of exposed container host ports.
- **Multi-Select & Bulk Actions**: Select multiple containers for simultaneous operations.
- **Status Filters**: Quick filtering by `All`, `Running`, or `Stopped` status.

### Inspection & Debugging
- **Real-Time Logs Viewer**: Stream container logs with syntax highlighting, search, line wrapping, line numbers, and auto-scroll.
- **Interactive Container Shell Exec**: Execute custom shell commands inside running containers with presets and history.
- **Container File System Browser**: Explore container directory trees and preview text files.
- **Resource Inspector**: Searchable JSON and key-value metadata view.

### Storage & System Management
- **Image Management**: View local images, delete dangling or unused images.
- **Volume Management**: List volumes, driver details, and prune unused storage.
- **Network Inspector**: Browse networks, view drivers, scopes, and attached containers.
- **System Usage Dashboard**: Overall storage breakdown (`system df`), reclaimable space estimate, and one-click pruning for containers, images, volumes, and build cache.

## Prerequisites

- `docker` and/or `podman` CLI installed and accessible on PATH.
- OpenChamber version 1.22.0 or higher.

## Development

```bash
bun install
bun run bundle      # bundles panel/main.ts into panel/main.js
bun run type-check  # TypeScript type check without emitting
bun run pack        # creates containers-extension.zip ready for distribution
```

## License

MIT — see [LICENSE](./LICENSE). The original extension is MIT licensed by OpenChamber.
