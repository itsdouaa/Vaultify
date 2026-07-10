# ADR-0002: Domain Model Design

**Status:** Accepted

**Date:** 2026-07-10

## Context

Vaultify is a secure password manager intended to support multiple
types of secrets (logins, secure notes, credit cards, SSH keys,
licenses, Wi-Fi credentials, identities, and future secret types).

Before implementing storage, cryptography or the user interface, a
stable domain model is required to define the business concepts of the
application independently of any technical implementation.

The domain model serves as the foundation for all subsequent
architectural decisions.

## Decision

Vaultify adopts a Domain-Driven Design (DDD) inspired domain model.

The domain model represents business concepts only and remains
independent from:

- programming language;
- storage format;
- database;
- cryptographic algorithms;
- synchronization;
- serialization;
- user interface.

The model is organized into three categories:

- Entities
- Value Objects
- Enumerations

The following architectural decisions define the model.

### 1. Vault Boundary

The Vault is the root aggregate of the domain.

Every Entity, except User, belongs to exactly one Vault.

The User exists outside the Vault boundary and owns one or more Vaults.

### 2. Entity Identity

Entities possess their own identity and lifecycle.

Each Entity has a unique identifier and may evolve independently over
time.

### 3. Value Objects

Field and Metadata are modeled as Value Objects.

They have no independent identity and cannot exist without an owning
domain object.

### 4. Generic Entry Model

An Entry represents one logical secret.

Different secret categories are represented through EntryType rather
than different Entity classes.

Examples include:

- Login
- Secure Note
- Credit Card
- SSH Key
- API Key
- License
- Wi-Fi Credential

This keeps the domain extensible while maintaining a uniform structure.

### 5. Identity

Identity is modeled as an independent Entity rather than an EntryType.

An Identity represents reusable personal information that may be
referenced by Entries while remaining an independent domain concept.

### 6. Organization

Vaultify provides two complementary organization mechanisms.

Folders provide hierarchical organization.

Tags provide flexible many-to-many classification.

Both concepts are intentionally independent.

### 7. Metadata

Metadata is modeled as a reusable Value Object shared by multiple
Entities.

Metadata never represents user secrets and remains independent from
cryptography and persistence.

### 8. Relationships

The domain distinguishes three relationship semantics:

- owns
- contains
- references

Ownership expresses simple ownership between independent Entities.

Containment expresses composition.

References express non-owning associations.

## Consequences

### Advantages

- Clear separation between business rules and implementation.
- Extensible support for future secret types.
- Stable aggregate boundaries.
- High cohesion of domain concepts.
- Independent evolution of infrastructure.
- Improved maintainability.
- Easier testing.
- Easier documentation.
- Better long-term scalability.

### Trade-offs

- The generic Entry model requires additional validation depending on
  EntryType.
- Identity introduces an additional domain concept compared to modeling
  everything as Entries.
- Metadata must remain generic enough to be reused across multiple
  Entities.

## Alternatives Considered

### Model every secret type as its own Entity

Examples:

- Login
- CreditCard
- SSHKey
- SecureNote

Rejected because it would duplicate behavior, increase maintenance
costs and reduce extensibility.

---

### Model Identity as an EntryType

Rejected because Identity has its own lifecycle, reusable Fields,
Metadata and references from Entries.

---

### Create specialized Metadata classes

Examples:

- EntryMetadata
- VaultMetadata
- FolderMetadata

Rejected because the differences are contextual rather than structural.

A single reusable Metadata Value Object provides a simpler and more
consistent model.

## References

- docs/DomainModel.md
