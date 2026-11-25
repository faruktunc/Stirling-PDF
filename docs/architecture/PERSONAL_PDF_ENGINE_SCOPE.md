# Personal PDF Engine — Fork Scope

## Purpose

Run Stirling PDF as a self-hosted processing engine for the existing Laravel PDF Tools application and personal use. Laravel remains the application-facing layer; Stirling remains an independently deployable PDF processing service.

## Keep

- PDF page operations: merge, split, reorder, rotate, crop, remove and extract pages.
- PDF optimization, compression, repair and metadata operations.
- Office/image/PDF conversions supported by the engine.
- OCR and text/image extraction.
- Password protection and document security tools.
- Visual signature workflow: draw a signature, type a signature, upload a signature image, place it on a page, and retain/reuse saved visual signatures.
- Watermarks, stamps, page numbers, headers and footers.
- The existing processing API needed by Laravel.

## Disable, do not delete

Digital certificate signing is disabled by default through:

`stirling.features.digital-signature.enabled=false`

The following backend controller groups are conditional on this setting and remain in the source tree:

- Certificate-based PDF signing.
- Hardware/PKCS#11 certificate signing.
- Digital-signature validation.
- Digital-signature removal.
- RFC 3161 timestamping.
- Multi-party signing sessions.

To re-enable the backend endpoints for development/testing, set:

`STIRLING_FEATURES_DIGITAL_SIGNATURE_ENABLED=true`

This flag does not disable visual signature creation or placement. Saved visual signature management remains separate from certificate signing.

## Integration boundary

- Laravel owns application authentication, authorization, user experience, file lifecycle and job orchestration.
- Stirling owns PDF transformations and processing.
- Laravel communicates with Stirling over a private Docker network using its HTTP API.
- Do not expose the Stirling management/API port publicly; route requests through Laravel or a private reverse proxy.

## Cleanup policy

Remove a feature only after identifying its backend endpoints, frontend entry points, dependencies, tests and shared components. Preserve shared PDF primitives and visual-signature code. Prefer feature flags for capabilities that may be useful later.

## Initial implementation status

- [x] Add a default-off backend feature flag for digital certificate signing.
- [ ] Hide digital certificate signing tools in the frontend while preserving visual signing.
- [ ] Verify visual signature draw/type/upload/place/save flows.
- [ ] Build and run the core engine in Docker.
- [ ] Add a minimal Laravel-facing API integration profile.
- [ ] Inventory and remove unrelated SaaS, desktop and cloud-only features.
