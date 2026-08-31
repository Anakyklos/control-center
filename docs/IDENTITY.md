# Identity — Anakyklos Control Center

## Decision

The Control Center is the canonical user-facing administrative surface of the Anakyklos ecosystem.

Its technical/repository name remains **Anakyklos Control Center**, but the name presented to the user should normally be simply **Anakyklos**.

| Field | Decision |
| --- | --- |
| Repository | `Anakyklos/control-center` |
| Technical component name | Anakyklos Control Center |
| User-facing name | Anakyklos |
| Codename | None |
| Animal | None |
| Ecosystem role | Canonical administrative surface |
| Desktop launcher | Yes |
| Primary icon | Anakyklos ecosystem mark |

## Why it has no animal codename

The Control Center is not another organism inside the fauna. It is the surface through which the user sees and administers the ecosystem as a whole.

Giving it an animal identity would make the Anakyklos identity compete at the same semantic level as Katherine, Talpa, Tecer, Mouse Hub, and other fauna members.

The intended hierarchy is instead:

```text
                 ANAKYKLOS
                     |
          canonical system surface
                     |
       +-------------+-------------+
       |                           |
 applications/services         extensions
       |
   Anakyklos fauna
```

The Control Center therefore represents the **whole**, not an individual member.

## Visual identity

The desktop launcher should use the primary Anakyklos ecosystem mark.

Animal icons remain useful for individual fauna members displayed inside the Control Center, including headless services such as the Substrate/Talpa.

This creates a clear visual distinction:

- **Anakyklos mark** — ecosystem, Control Center, global administrative identity;
- **animal identities** — individual components with their own architectural identity;
- **technical/service identifiers** — implementation names used where appropriate.

## Relationship with fauna

The Control Center should be capable of showing fauna members regardless of whether they have standalone graphical interfaces.

This is especially important for headless components such as the Anakyklos Substrate (`Talpa`): the component may have its own animal identity without gaining a desktop launcher.

The Control Center is therefore the natural home for status, configuration, permissions, health, diagnostics, and identity of Anakyklos components that do not warrant standalone interfaces.
