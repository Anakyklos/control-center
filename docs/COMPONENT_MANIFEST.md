# Component Manifest Contract

## Status

**Architectural direction / pre-implementation contract.**

This document defines the minimum information an Anakyklos component should be able to publish so that the Control Center can discover and administer it without hard-coding project-specific UI logic.

The exact serialization format and protocol versioning remain implementation decisions. TOML is used below only as a readable example.

## Goals

A component manifest should allow the Control Center to discover:

- stable component identity;
- human-readable name and optional codename;
- component class;
- version;
- whether a desktop launcher exists;
- whether the component is expected to run in the background;
- capabilities;
- permissions requested;
- health/status provider;
- administrative actions;
- configuration schema/provider;
- IPC or integration endpoints;
- resource-policy support.

The contract must be sufficiently generic that adding a new Anakyklos project does not require modifying Control Center source code merely to make that component visible.

## Component classes

Initial classes:

- `application` — has a primary user experience;
- `service` — headless background/system component;
- `extension` — optional capability, adapter, plugin, bridge, or integration.

Additional classes should be introduced only when they represent a durable architectural distinction.

## Example: application

```toml
[component]
id = "mouse-hub"
name = "Mouse Hub"
codename = "mus"
type = "application"
version = "0.1.0"
launcher = true

[capabilities]
input_devices = true
automation = true

[configuration]
provider = "unix-socket"

[health]
provider = "unix-socket"

[permissions]
requested = ["input.observe", "input.control"]
```

## Example: service

```toml
[component]
id = "anakyklos-substrate"
name = "Anakyklos Substrate"
type = "service"
version = "0.1.0"
launcher = false

[capabilities]
system_events = true
device_events = true
resource_control = true

[configuration]
provider = "unix-socket"

[health]
provider = "unix-socket"

[permissions]
requested = ["system.observe", "resources.control"]
```

## Required design properties

### Stable identity

`component.id` must be stable across versions. Display names and codenames may evolve; IDs are contracts.

### No arbitrary UI injection by default

The preferred model is **schema-driven rendering**. Components expose settings and actions through typed metadata rather than injecting arbitrary web/UI code into the Control Center.

If custom UI extensions are ever supported, they must be isolated and explicitly permissioned.

### Capability-based permissions

Permissions should describe what a component may do rather than which internal implementation it happens to call.

Examples:

```text
input.observe
input.control
audio.observe
audio.control
system.observe
process.observe
process.control
resources.observe
resources.control
network.observe
filesystem.observe
```

The permission namespace should eventually live in a shared Anakyklos contract specification.

### Health is not telemetry

A health provider should expose enough information to answer whether a component is available and functioning. It must not require high-frequency telemetry collection while the Control Center is closed.

### Actions are explicit

Administrative actions such as `start`, `stop`, `restart`, `open`, `diagnostics`, or `reconnect` should be declared and typed. The Control Center must not guess shell commands from component names.

## Discovery

The final discovery mechanism is not fixed yet. Acceptable directions include:

- manifests installed into a standard Anakyklos directory;
- registration through the Substrate or another common registry;
- systemd metadata plus an Anakyklos manifest;
- a combination of static identity and runtime capability discovery.

Discovery must remain local-first and inexpensive.

## Versioning

The manifest contract must gain an explicit schema version before implementation ships.

Consumers must reject or gracefully degrade unsupported schema versions rather than silently interpreting incompatible fields.

## Boundary

The manifest tells the Control Center **what a component is and how it can be administered**. It does not move business logic from that component into the Control Center.
