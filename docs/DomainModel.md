# Domain Model

## Purpose

This document defines the core business concepts of Vaultify.

The domain model is independent from:

- Programmation language implementation
- Storage format
- Cryptography
- User interface

It describes **what** Vaultify manages, not **how** it manages it.

# Classification

## Entities

- User
- Vault
- Entry
- Folder
- Tag
- Attachment
- History
- Identity

## Value Objects

- Field
- Metadata

## Enumerations

- EntryType
- FieldType
- Sensitivity

# Common Characteristics

Unless explicitly stated otherwise, every entity:

- has a unique identifier;
- has a single responsibility;
- belongs directly or transitively to exactly one Vault
- may contain Metadata;
- can be serialized independently from storage format;
- is independent from cryptography and UI.

### Exception

`User` does not belong to a Vault. Instead, a Vault belongs to a User
(see User invariants). User is the only Entity that sits above the
Vault boundary rather than inside it.

# Entities

## User

### Definition

A User represents the owner of one or more Vaults.

A User defines preferences, authentication methods and
application-level settings independently from the stored secrets.

### Attributes

- Identifier
- Username
- Display Name
- Email Address
- Authentication Methods
- Preferences
- Security Settings
- Vaults
- Metadata

### Responsibility

- Own Vaults.
- Manage authentication methods.
- Store user preferences.
- Store security preferences.

### Out of Scope

A User is NOT responsible for:

- storing secrets
- encrypting Vaults
- managing Entries

### Invariants

- A User may own one or more Vaults.
- Every Vault belongs to exactly one User.
- A User cannot access another User's Vaults.

## Vault

### Definition

A Vault is the root container of all secure data.

A Vault owns every object stored by the user.

### Attributes

- Identifier
- Name
- Description
- Owner
- Entries
- Folders
- Tags
- Identities
- Metadata

### Responsibility

The Vault is the root aggregate of the domain.

It owns every domain entity and represents one secure collection of user data.

It provides the logical boundary within which all entities exist.

### Out of Scope

The Vault is NOT responsible for:

- encryption
- key derivation
- authentication
- persistence
- synchronization

### Invariants

- Every entity, except User, belongs to exactly one Vault.
- A Vault cannot exist without an owning User.
- A Vault always exists before any entity it contains.
- A Vault is the root of the domain model for all entities except User.

## Entry

### Definition

An Entry represents one logical secret stored by the user.

### Attributes

- Identifier
- EntryType
- Fields
- Attachments
- History
- Metadata

### Responsibility

An Entry represents one logical secret and owns all information required to describe that secret.

### Out of Scope

Entry is NOT responsible for:

- encryption
- persistence
- synchronization
- authentication

### Invariants

- Every Entry belongs to exactly one Vault.
- Every Entry has exactly one EntryType.
- An Entry may contain one or more Fields.
- An Entry may contain zero or more Attachments.
- An Entry may contain zero or more History records.
- An Entry always owns its Metadata.
- An Entry cannot exist without its Vault.

### Examples of Types:

- Login credential
- Secure note
- Bank card
- SSH key
- Wi-Fi credential

## Folder

### Definition

A Folder provides a hierarchical organization mechanism for Entries.

### Attributes

- Identifier
- Name
- Parent Folder
- Child Folders
- Entries
- Metadata

### Responsibility

A Folder organizes Entries into a logical hierarchy.

Folders improve organization only; they do not affect security or ownership.

### Out of Scope

A Folder is NOT responsible for:

- encrypting Entries
- storing Entries
- enforcing permissions

### Invariants

- Every Folder belongs to exactly one Vault.
- A Folder may contain zero or more Entries.
- A Folder may have zero or one parent Folder.

## Tag

### Definition

A Tag is a label assigned to Entries.

An Entry may have multiple Tags.

### Attributes

- Identifier
- Name
- Color
- Description
- Metadata

### Responsibility

A Tag provides a flexible classification mechanism for Entries.

Tags allow multiple organizational views independent of Folder structure.

### Out of Scope

A Tag is NOT responsible for:

- grouping Entries physically
- changing Entry content

### Invariants

- A Tag belongs to exactly one Vault.
- A Tag may be assigned to multiple Entries.
- An Entry may have multiple Tags.

## Attachment

### Definition

An Attachment is a binary object linked to an Entry.

### Attributes

- Identifier
- File Name
- File Type
- File Size
- Content

### Responsibility

An Attachment represents binary data associated with one Entry.

### Out of Scope

An Attachment is NOT responsible for:

- rendering its content
- decrypting itself
- managing file storage

### Invariants

- Every Attachment belongs to exactly one Entry.
- An Attachment cannot exist independently.

### Examples:

- PDF
- Image
- Certificate
- Private key

## History

### Definition

History stores previous versions of an Entry.

### Attributes

- Identifier
- Version Number
- Snapshot
- Creation Date

### Responsibility

History preserves previous immutable versions of an Entry for recovery and auditing.

### Out of Scope

History is NOT responsible for:

- conflict resolution
- synchronization
- version merging

### Invariants

- Every History record belongs to exactly one Entry.
- History records are immutable.
- History cannot exist without its Entry.

## Identity

### Definition

An Identity represents a person's information that can be reused across multiple entries.

### Attributes

- Identifier
- Fields
- Metadata

### Design Note

- Structurally, an Identity is composed of Fields, the same way an Entry is.
This keeps the model consistent: both Entry and Identity are "containers of Fields," but serve different purposes (a secret vs. reusable personal data).

### Responsibility

Represents a person's identity as a secure domain entity.

An Identity groups personal information that can be securely
stored, managed and reused across different contexts while
remaining independent from authentication and credentials.

### Out of Scope

An Identity is NOT responsible for:

- authenticating a User
- verifying the accuracy of personal information
- managing passwords or credentials
- encryption
- persistence
- synchronization

### Invariants

- Every Identity belongs to exactly one Vault.
- Every Identity contains one or more Fields.
- Every Identity owns exactly one Metadata object.
- An Identity cannot exist without its Vault.

### Examples

- Name
- Address
- Phone number
- Passport

# Value Objects

Value Objects describe immutable domain concepts that have no
independent identity.

They are defined solely by their attributes and always exist
as part of another domain object.

## Field

### Definition

A Field represents one individual piece of information contained
within an Entry.

A Field is the smallest unit of user data in the domain model.

### Attributes

- Name
- Value
- FieldType
- Sensitivity

#### FieldType

Defines the logical type of the stored value.

Examples:
- Text
- Number
- Date
- URL
- Email
- Phone Number
- Enumeration

#### Sensitivity

Defines whether the value should be treated as sensitive
by the application.

Examples:

Sensitive:
- Password
- PIN
- Recovery Code
- Private Key

Non-sensitive:
- Username
- Website URL
- Email Address

### Characteristics

- A Field has no identity.
- A Field exists only as part of an Entry.
- A Field represents exactly one logical value.
- A Field is independent of encryption and storage mechanisms.

### Validation Rules

- Every Field must have a Name.
- Every Field must have a FieldType.
- A Value may be empty unless restricted by its owning Entry.

### Examples:

- Username
- Password
- URL
- Expiration Date
- PIN

## Metadata

### Definition

Metadata represents contextual information associated with a
domain object.

Metadata describes the object itself rather than the user secret
it contains.

Metadata is a reusable Value Object that may be associated with
an Entity whenever contextual information is required.

### Attributes

Metadata is intentionally generic.

The exact attributes depend on the Entity that owns it.

Examples include:

- Creation Date
- Last Modification Date
- Last Access Date
- Favorite Flag

### Characteristics

- Metadata has no identity.
- Metadata cannot exist independently.
- Metadata belongs to exactly one domain object.
- Metadata never represents user secrets.
- Metadata is independent of cryptography, persistence and presentation.

### Validation Rules

- Metadata cannot exist without an owning object.
- Creation Date, when present, cannot change.
- Last Modification Date cannot be earlier than Creation Date.
- Last Access Date, when present, cannot be earlier than Creation Date.

### Examples

Entry Metadata
- Creation Date
- Last Modification Date
- Favorite Flag

Vault Metadata
- Creation Date
- Description

Folder Metadata
- Creation Date
- Last Modification Date

# Enumerations

## EntryType

### Purpose

Defines the category of an Entry.

EntryType determines the semantic meaning of an Entry and guides
its interpretation and presentation by the application.

### Values

- Login
- Secure Note
- Credit Card
- SSH Key
- API Key
- License
- Wi-Fi Credential

## FieldType

### Purpose

Defines the logical type of a Field value.

FieldType influences validation and presentation but does not
change the meaning of the stored data.

### Values

- Text
- Number
- Date
- URL
- Email
- Phone Number
- Enumeration
- Boolean

## Sensitivity

### Purpose

Indicates whether the value of a Field should be treated as
sensitive by the application.

Sensitivity is a logical property independent of the underlying
cryptographic implementation.

### Values

- Sensitive
- NonSensitive

# Naming Conventions

- Entity names are singular.
- Value Object names are singular.
- Enumeration names are singular.
- Relationships are expressed using active verbs.

# Relationships

## Ownership Hierarchy

User "1" --- "0..*" Vault
Vault "1" --- "0..*" Entry
Vault "1" --- "0..*" Folder
Vault "1" --- "0..*" Tag
Vault "1" --- "0..*" Identity

## Entry Composition

Entry "1" --- "1..*" Field
Entry "1" --- "0..*" Attachment
Entry "1" --- "0..*" History
Entry "1" --- "1" Metadata

## Entry ↔ Tag

Entry "0..*" --- "0..*" Tag : tagged with

## Entry ↔ Folder

Folder "0..1" --- "0..*" Entry : contains

## Entry ↔ Identity

Entry "0..*" --- "0..1" Identity : references

## History ↔ Attachment

History "1" --- "0..*" Attachment : contains

## Identity Composition

Identity "1" --- "1..*" Field
Identity "1" --- "1" Metadata

## Folder Structure

Folder "0..1" --- "1" Folder    (parent)
Folder "1" --- "0..*" Folder    (children)

## Metadata Ownership (generic)

User "1" --- "1" Metadata
Vault "1" --- "1" Metadata
Entry "1" --- "1" Metadata
Identity "1" --- "1" Metadata
Folder "1" --- "1" Metadata
Tag "1" --- "1" Metadata

## Relationship Terminology

- owns: expresses simple ownership.
- contains: expresses structural containment.
- references: expresses a non-owning association.

```mermaid
classDiagram
direction TB

class User
class Vault
class Entry
class Identity
class Folder
class Tag
class Attachment
class History
class Field
class Metadata

%%========================
%% Ownership Hierarchy
%%========================

User "1" --> "0..*" Vault : owns

Vault "1" *-- "0..*" Entry : contains
Vault "1" *-- "0..*" Folder : contains
Vault "1" *-- "0..*" Tag : contains
Vault "1" *-- "0..*" Identity : contains

%%========================
%% Entry Composition
%%========================

Entry "1" *-- "1..*" Field : contains
Entry "1" *-- "0..*" Attachment : contains
Entry "1" *-- "0..*" History : contains
Entry "1" *-- "1" Metadata

%%========================
%% Entry Relationships
%%========================

Entry "0..*" --> "0..*" Tag : tagged with
Folder "0..1" --> "0..*" Entry : contains
Entry "0..*" --> "0..1" Identity : references

%%========================
%% History
%%========================

History "1" *-- "0..*" Attachment : contains

%%========================
%% Identity Composition
%%========================

Identity "1" *-- "1..*" Field : contains
Identity "1" *-- "1" Metadata

%%========================
%% Folder Structure
%%========================

Folder "0..1" --> "1" Folder : parent
Folder "1" --> "0..*" Folder : children

%%========================
%% Generic Metadata Ownership
%%========================

User "1" *-- "1" Metadata
Vault "1" *-- "1" Metadata
Folder "1" *-- "1" Metadata
Entry "1" *--- "1" Metadata
Identity "1" *--- "1" Metadata
Tag "1" *-- "1" Metadata
```

### Diagram Legend

- `-->` Association (reference)
- `*--` Composition (strong ownership)
