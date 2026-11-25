# Personal PDF Engine — Fork Scope

## Purpose

Use this fork as a self-hosted PDF processing engine for personal use and for the existing Laravel PDF Tools application. Laravel remains the application-facing layer; Stirling runs as an independent service.

## Tools retained in the personal UI

- **Page operations:** merge, split, reorder, crop, rotate, auto-rotate, resize/scale, extract and remove pages, remove blank pages, add page numbers.
- **Editing and review:** PDF text editor, read/view, annotations, add text, add images, edit table of contents/bookmarks.
- **Signatures:** visual signatures only — draw, type or upload an image, position it on a page, and use browser-local saved signatures.
- **Document security:** add/remove password, change permissions, sanitize, redact and flatten.
- **Optimization and repair:** compression and repair.
- **Conversion and extraction:** PDF/Office/image conversion, OCR, extract text/images, manage attachments.
- **Other useful operations:** watermark, image/text stamp, overlay PDFs, compare PDFs, inspect metadata and document information.
- **Automation:** retain the multi-step automation tool.

The UI registry uses an explicit allowlist. Tools outside this list are not shown in the personal UI. Their source files are not deleted as part of this first reduction pass.

## Digital certificate signing

Digital certificate signing is disabled by default and is not exposed in the personal UI. The implementation remains in the source tree for possible future reactivation.

The backend flag is:

`stirling.features.digital-signature.enabled=false`

Set `STIRLING_FEATURES_DIGITAL_SIGNATURE_ENABLED=true` to re-enable the guarded backend endpoints. The UI allowlist must also be updated before the certificate-signing tools become visible.

The following remain disabled: certificate signing, hardware/PKCS#11 signing, digital-signature validation/removal, RFC 3161 timestamping and multi-party signing sessions.

Visual signing is separate. The `sign` tool and its client-side drawing/text/image workflow remain enabled.

## Personal deployment profile

The default Compose profile:

- Builds with `STIRLING_FLAVOR=core`.
- Excludes the additional/proprietary backend flavor.
- Omits the admin/processor portal from the embedded frontend build.
- Disables login because this is a single-user personal instance.
- Binds port 8080 to host loopback only; do not expose this unauthenticated service directly to the Internet.
- Disables metrics, Google visibility and surveys.
- Uses Turkish as the default locale.
- Keeps OCR data, configuration and logs in persistent host directories.

Start it from `docker/compose`:

```bash
docker compose up -d --build
```

Health endpoint: `http://127.0.0.1:8080/api/v1/info/status`.

For a Laravel container to call the engine privately, attach that container to the Docker network `personal-pdf-network` and use `http://personal-pdf-engine:8080`. A Laravel process running directly on the host can use `http://127.0.0.1:8080`.

## Integration boundary

- Laravel owns application authentication, authorization, user experience, file lifecycle and job orchestration.
- Stirling owns PDF transformations and processing.
- Keep the engine port private. Laravel should be the public-facing entry point.
- Use the HTTP API for file processing; do not couple Laravel to Stirling's internal Java classes.

## Cleanup policy

The current reduction is intentionally UI-first: it removes unselected tools from the user-facing registry without deleting shared backend classes or dependencies. Further backend and image-size cleanup must be based on build/dependency analysis and regression tests, not broad directory deletion.

## Implementation status

- [x] Default-off backend feature flag for digital certificate signing.
- [x] Guard the certificate-signature removal endpoint with the same flag.
- [x] Keep visual signature creation/placement in the UI.
- [x] Add an explicit personal-tool allowlist.
- [x] Set Docker build defaults to the core flavor and omit the portal.
- [x] Add a private, single-user Docker Compose profile.
- [ ] Run Gradle and frontend production builds.
- [ ] Run Docker Compose health check and smoke-test retained tools.
- [ ] Verify visual signature draw/type/image/place/save flows in the running image.
- [ ] Add and test Laravel API integration.
