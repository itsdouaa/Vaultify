# Domain Model

## Purpose

This document defines the core business concepts of Vaultify.

The domain model is independent from:

- Programmation language implementation
- Storage format
- Cryptography
- User interface

It describes **what** Vaultify manages, not **how** it manages it.

# Principles

- Every object has a single responsibility.
- Objects must be independent from storage.
- Objects must not depend on cryptography.
- The UI must not define the domain.
- The domain should remain stable over time.

## Vault

### Definition

A Vault is the root container of all secure data.

A Vault owns every object stored by the user.

There is exactly one unlocked Vault instance during a session.

## Entry

### Definition

An Entry represents one logical secret stored by the user.

Examples:

- Website account
- Bank card
- Secure note
- SSH key
- Wi-Fi password

## Field

### Definition

A Field represents one individual piece of information
inside an Entry.

Examples:

- Username
- Password
- URL
- Expiration Date
- PIN

## Identity

### Definition

An Identity represents a person's information that can
be reused across multiple entries.

Examples:

- Name
- Address
- Phone number
- Passport

## Attachment

### Definition

An Attachment is a binary object linked to an Entry.

Examples:

- PDF
- Image
- Certificate
- Private key

## Folder

### Definition

A Folder groups Entries for organization purposes.

## Tag

### Definition

A Tag is a label assigned to Entries.

An Entry may have multiple Tags.

## History

### Definition

History stores previous versions of an Entry.

## Metadata

### Definition

Metadata contains information about an object that is
not considered part of the secret itself.

Examples:

- Creation date
- Last modification
- Favorite flag
