# Control Center Architecture

## Role

The Control Center is the **canonical administrative UI for Anakyklos**.

It exists because the ecosystem contains components with different interaction models:

- applications with their own interfaces;
- background services with no primary UI;
- extensions and bridges that need configuration but not launchers;
- privileged system capabilities that need clear permission and policy surfaces.

Without a common administrative layer, each project would eventually duplicate installation state, autostart settings, permissions, integration configuration, diagnostics, and resource controls.

## Non-goals

The Control Center must not become:

- a second Ouroboros;
- a replacement for application-specific primary interfaces;
- a privileged Linux daemon;
- a permanent high-frequency monitoring dashboard;
- a monolith containing the internal logic of every Anakyklos project;
- a generic shell-command launcher disguised as configuration UI.

## Responsibility boundaries

### Control Center owns

- installed-component inventory;
- component classification and presentation;
- administrative configuration surfaces;
- ecosystem-level resource policy UI;
- permission presentation and approval/revocation flows;
- integration status and configuration;
- health and diagnostics presentation;
- autostart/lifecycle controls exposed through stable contracts;
- developer-oriented inspection tools;
- user-facing discovery of headless services.

### Components own

- their domain/business logic;
- their runtime state;
- their component-specific configuration semantics;
- their own primary UX when they are applications;
- validating requested configuration values before applying them.

### Substrate owns

- low-level Linux event collection;
- device/system integration;
- cgroup/resource primitives;
- privileged system operations through narrowly scoped interfaces;
- local machine event transport exposed to trusted Anakyklos consumers.

### Ouroboros owns

- higher-level orchestration;
- missions and execution lifecycle;
- decomposition/coordination of work;
- orchestration-specific persistence and validation.

## User-facing model

### Overview

The overview answers whether the ecosystem is healthy and highlights actionable problems. It should avoid decorative telemetry.

Representative information:

```text
Anakyklos

System                         Healthy
--------------------------------------
Katherine                      Active
Ouroboros                      Idle
Runstead                       Stopped
lifeOS                         Active

Infrastructure
Substrate                      Active
Colab Infinity                 Disconnected
```

### Applications

Shows user-facing applications and their administrative controls, such as:

- open;
- enable/disable integration;
- autostart;
- configure;
- uninstall, when supported by the installation model.

### Services

Shows headless components such as Substrate, Ouroboros Runtime, bridges, workers, and indexers.

A service may have an icon/identity inside the Control Center while remaining absent from the desktop launcher menu.

### Integrations

Shows durable relationships between components. This may eventually include a graph view, but the architecture must not require a graph renderer to function.

Examples:

```text
Katherine -> Ouroboros
Ouroboros -> Runstead
521C -> Substrate
Mouse Hub -> Substrate
Colab Infinity -> Ouroboros
```

### Resources

Provides ecosystem-level and component-level policy rather than merely displaying metrics.

Potential controls:

- memory ceilings;
- CPU priorities/quotas;
- behavior on battery;
- worker limits;
- GPU policy;
- remote-compute policy;
- Colab Infinity connectivity.

Actual enforcement belongs to the appropriate lower-level service, not the UI.

### Permissions

The UI should make privileged capabilities understandable per component.

Example:

```text
Mouse Hub
  allowed   input.observe
  allowed   input.control
  denied    network.observe

521C
  allowed   audio.control
  allowed   bluetooth
  denied    filesystem.observe
```

The final permission vocabulary must be shared and versioned.

### Development

May expose:

- component versions;
- manifests;
- health checks;
- IPC endpoint status;
- recent important events;
- logs on demand;
- diagnostic actions.

Developer tooling must not force production installations to retain large telemetry histories.

## State and communication

The Control Center should prefer local IPC and typed contracts.

Preferred properties:

- local-first;
- event-driven;
- no mandatory cloud dependency;
- bounded memory usage;
- lazy detail loading;
- explicit timeouts;
- no hidden retry storms;
- no privileged execution in the GUI process unless absolutely unavoidable.

Unix domain sockets are a natural initial transport candidate for local runtime communication, but the contract should remain transport-conscious rather than tightly coupling product semantics to one library.

## UI stack decision

The UI implementation stack is intentionally **not frozen by this document**.

It must be selected against measurable requirements:

- low idle RAM/CPU;
- good native Linux behavior;
- maintainable packaging;
- fast startup;
- accessibility;
- robust IPC integration;
- minimal runtime footprint;
- ability to build a polished settings-style desktop experience.

The ecosystem architecture must remain valid if the UI technology changes later.

## Packaging and launcher policy

The Control Center is a first-class desktop application and should have one launcher entry named **Anakyklos**.

Headless services managed by it should not create launcher entries merely because they have icons or internal configuration surfaces.

## Evolution rule

A new component should become visible primarily by publishing the standard component contract, not by adding a bespoke `if component == ...` branch to the Control Center.
