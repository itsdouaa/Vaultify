# ADR-0001: Use a Cargo Workspace

- **Status:** Accepted
- **Date:** 2026-07-05

## Context

Vaultify is intended to become a cross-platform password manager with multiple
applications sharing the same secure core.

The project is expected to include:

- a reusable core library
- a command-line interface
- a desktop application
- possible future mobile applications

A project structure is needed that allows sharing code while keeping clear
boundaries between components.

## Decision

Use a Cargo Workspace as the root project structure.

The workspace will initially contain:

- vault-core
- vault-cli

Additional crates may be added in the future without changing the overall
architecture.

## Consequences

### Advantages

- Clear separation of responsibilities.
- Shared dependency management.
- Easier testing.
- Scalable architecture.
- Suitable for open-source development.

### Disadvantages

- Slightly more complex project structure.
- Requires understanding Cargo workspaces.

## Alternatives Considered

### Single crate

Rejected because the project is expected to grow and multiple applications
will reuse the same core library.

### Multiple independent repositories

Rejected because it complicates maintenance and versioning.
