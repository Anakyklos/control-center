# Anakyklos Control Center

The **Anakyklos Control Center** is the canonical administrative surface for the Anakyklos ecosystem.

Its repository and technical component name is `control-center`. In the desktop environment and other user-facing surfaces, the application should normally be presented simply as **Anakyklos**.

## Identity

- **User-facing name:** `Anakyklos`
- **Codename:** none
- **Animal identity:** none
- **Desktop launcher:** yes
- **Primary icon:** the Anakyklos ecosystem mark

The Control Center deliberately does **not** belong to the fauna. It represents the ecosystem as a whole and is the surface through which the user sees and administers individual fauna members, applications, services, and extensions.

See [`docs/IDENTITY.md`](docs/IDENTITY.md) for the identity decision and rationale.

## Purpose

Anakyklos is growing as a collection of applications, services, runtimes, bridges, workers, and integrations. Not every component should have its own settings UI or desktop launcher.

The Control Center provides one place to answer:

- What Anakyklos components are installed on this machine?
- Which applications and background services are active?
- How are components integrated with one another?
- Which permissions and capabilities does each component have?
- How much CPU, memory, I/O, GPU, and remote compute may the ecosystem use?
- Which components start automatically?
- Is the installation healthy?
- Where are diagnostics and development information exposed?

The Control Center is therefore closer to **system settings + ecosystem administration** than to a productivity application.

## User-facing identity

- **Desktop name:** `Anakyklos`
- **Repository:** `Anakyklos/control-center`
- **Type:** desktop application
- **Launcher:** yes
- **Primary icon:** the Anakyklos ecosystem mark, not an individual animal codename

Individual projects may retain their own animal identities. The Control Center represents the ecosystem as a whole and should use the Anakyklos identity instead.

## Product model

Anakyklos components are divided into three broad classes.

### Applications

User-facing software with its own primary experience. Examples include Katherine, Runstead, lifeOS, Tecer, 521C, and Mouse Hub.

Applications may have their own launcher and UI.

### Services

Headless components with a functional role but no primary standalone UI. Examples include Ouroboros Runtime, the Anakyklos Substrate, indexers, bridges, and workers.

Services should normally be managed through the Control Center instead of appearing in the desktop application menu.

### Extensions

Optional capabilities such as provider adapters, plugins, remote-compute bridges, specialized agents, and hardware integrations.

Extensions are installed or enabled independently but are surfaced and administered through the Control Center when appropriate.

## Initial navigation model

The first product architecture should be organized around these areas:

1. **Overview** — ecosystem health and important status.
2. **Applications** — installed user-facing Anakyklos applications.
3. **Services** — background/headless components.
4. **Integrations** — relationships and communication paths between components.
5. **Resources** — global and per-component resource policies.
6. **Permissions** — capability grants and privileged operations.
7. **Automation** — ecosystem-level automation surfaces where appropriate.
8. **Development** — diagnostics, manifests, versions, logs, events, and developer information.
9. **System** — Control Center and ecosystem-level settings.

The UI must remain useful without becoming a permanently running telemetry dashboard. Detailed information should be collected or rendered on demand whenever possible.

## Architectural principles

### 1. The Control Center is not the orchestrator

Ouroboros remains responsible for mission orchestration and higher-level execution. The Control Center exposes administration and observability; it must not absorb Ouroboros responsibilities.

### 2. The Control Center is not the system daemon

Low-level Linux integration belongs to [`Anakyklos/substrate`](https://github.com/Anakyklos/substrate). The Control Center consumes stable interfaces exposed by system services instead of directly reimplementing `udev`, cgroups, netlink, filesystem watching, or privileged operations.

### 3. Components describe themselves

The Control Center should not contain a hard-coded settings page for every Anakyklos project.

Each component should publish a machine-readable manifest describing its identity, type, capabilities, health surface, actions, configuration schema, permissions, and integration endpoints. The Control Center discovers these manifests and renders a consistent administrative experience.

See [`docs/COMPONENT_MANIFEST.md`](docs/COMPONENT_MANIFEST.md).

### 4. Headless components stay headless

A component may have an icon and visual identity without having a desktop launcher. Services should surface inside Anakyklos rather than polluting the desktop application menu.

For example, the Substrate belongs to the fauna under the codename **Talpa**, while remaining a headless service managed from Anakyklos.

### 5. Low idle cost is a requirement

The Control Center should not require expensive polling when closed. It should favor event-driven state, lazy loading, and on-demand diagnostics.

## Relationship with Substrate

```text
User
  |
  +-- Anakyklos applications
  |
  `-- Anakyklos Control Center
             |
             | administration / status / policy
             v
       stable Anakyklos contracts
             |
       +-----+------+
       |            |
   Ouroboros     Substrate / Talpa
                    |
             Linux interfaces
                    |
                 kernel
```

The Control Center is the **human-facing administrative layer**. The Substrate is the **machine-facing operating-system integration layer**.

## Status

This repository currently establishes the architectural and product foundation. Implementation choices for the desktop UI stack should be evaluated separately against Anakyklos-wide requirements for low memory, low CPU usage, modularity, native Linux behavior, and maintainability.
