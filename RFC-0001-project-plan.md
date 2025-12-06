# My Cloud Config — Project Plan (v1)

## 1. Purpose

A unified lightweight storage for configuration files:

* Client-side encryption.
* Versioning.
* A minimal protocol with an extensible architecture.

## 2. Overall Architecture

The project consists of four main components:

1. **Protocol (standard)** — definition of formats and rules.
2. **SDK** — client libraries for interacting with the protocol (MVP: Go).
3. **Server** — implementation using PostgreSQL.
4. **CLI** — tool for interacting with the server and configuration packages.

Storage adapters, documentation, testing, and development workflow are also defined.

## 3. Definitions / Terminology

**Envelope**
A structured container that includes metadata and binary content of a configuration object. May be encrypted or plaintext.

**Config object**
The original user file stored in the system.

**Version**
A monotonically increasing identifier of a config object revision.

**Latest**
A reference to the most recent version.

**Protocol**
A set of rules, formats, and guarantees defining how clients interact with servers.

**SDK**
A client-side library implementing protocol operations.

**Server**
A backend service providing API for uploading, storing, and retrieving configs.

**Storage Adapter**
An abstraction layer for storing blobs and metadata (PostgreSQL, S3, filesystem, etc.).

**AEAD**
Authenticated Encryption with Associated Data.

**KDF**
A function deriving a cryptographic key from a password.

## 4. Out of Scope (for MCC v1)

The following areas are intentionally excluded from the MVP:
* Web UI.
* Access control models (ACL, RBAC, sharing, delegation).
* Federation between multiple servers.
* Validation or interpretation of config file contents.
* Advanced observability (tracing, APM, alerting).
* Public configs, aliases, temporary configs.
* Complex TTL or garbage collection policies.
* Multiple storage backends at once (MVP: PostgreSQL only).
* Advanced rate limiting (only basic limits allowed).

## 5. MCC Protocol v1

A minimal specification, independent of the server implementation.

### 5.1. Envelope format

* Structure: metadata + data.
* Encryption type and parameters included inside the package.
* Integrity check included.
* Protocol version embedded in the package.

### 5.2. Retrieval methods

* Fetching latest.
* Fetching a specific version.

### 5.3. Versioning rules

* Versions increment monotonically.
* If version is not specified, latest is assumed.

### 5.4. Protocol error codes

* Errors are grouped into: protocol, access, version, envelope format.

## 6. SDK

A minimal library that works with the protocol.

### 6.1. MVP features

* Download a config via URL.
* Decode the envelope.
* Decrypt the object.

### 6.2. Implementation

* Language: Go.
* Follows protocol rules only, no dependency on server implementation.
* Suitable for external usage.

### 6.3. Encryption

* KDF: PBKDF2-HMAC-SHA256.
* AEAD: AES-256-GCM.
* Salt included.

### 6.4. Extensibility

* Support for additional KDF/AEAD in future versions.

## 7. Server

A reference backend implementation.

### 7.1. Required features

* User registration.
* Authentication (login-password, JWT).
* Config upload.
* Fetch config (latest or specific).
* List configs.

### 7.2. Storage

* Metadata stored in PostgreSQL.
* PostgreSQL adapter stores config blobs.

### 7.3. Configuration & logging

* Config file or environment variables.
* docker-compose support (server + PostgreSQL).
* Basic error logging.

### 7.4. Correctness & security

* Server never decrypts private configs.
* Passwords stored as hashes only.
* File size limit, file count limit, rate limits.

### 7.5. API

* Server API is documented separately; not part of the protocol.

### 7.6. Extensibility

* Ability to add additional storage adapters.

## 8. CLI

### 8.1. MVP functionality

* User login.
* Upload config.
* Download & decrypt.
* List configs.
* Delete configs.

## 9. Versioning & Releases

### 9.1. Protocol

* Semantic versioning.
* Protocol changes require a new major version.

### 9.2. SDK / Server / CLI

* Independent versioning aligned with the protocol.
* git tag for every release.

## 10. Compatibility

* Protocol is stable within a major version.
* SDK must tolerate unknown fields (forward-compatible).

## 11. Testing

* Unit tests.
* Integration tests.
* Basic load tests (if possible).
* Minimum coverage for core logic: 30%.

## 12. Dev Workflow

### 12.1. Branching

* `main` contains stable releases.
* Development occurs in `feature/<name>` or `fix/<name>` branches.
* All changes merged via PRs.

### 12.2. Commits

* Conventional Commits (`feat:`, `fix:`, `docs:` etc.).

### 12.3. Pull Requests

* Small PRs.
* PRs must include description.
* Tests must pass.

### 12.4. Formatting & Linting

* Code must pass `go fmt`.
* golangci-lint is used.

### 12.5. Testing

* Unit tests mandatory for core logic.
* Integration tests via docker-compose.

### 12.6. Tooling

* Unified workflow via `make` (`make build`, `make test`, `make lint`).

## 13. Documentation

* MCC Protocol v1.
* SDK usage guide.
* Server installation & configuration.
* CLI guide.
* Quickstart tutorial.

## 14. Minimal Metrics

* Number of uploads.
* Number of users.

## 15. License

* Apache 2.0.

## 16. Roadmap

* Public configs (plaintext).
* Public aliases.
* Temporary configs (no versions, expires_at, auto-deletion).
* Additional storage adapters.
* SDKs for more languages.
* Web UI.
* Version tags beyond latest.

## 17. Developer Diary

Optional log of progress, decisions, thoughts.
