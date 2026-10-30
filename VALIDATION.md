# Validation — 2026-10-29

Validated against the official Hermes Agent v0.22.5 source at commit `bf0203a7` using its `hermes_cli.plugin_validate.validate_plugin_dir` catalog-admission implementation in an isolated Python 3.14 environment. Also passed against main at `088f657ecf77674c7b8e2ededf4e3576d82e042c`.

- Portable manifest, fields, and config schema: passed.
- Server trust: `untrusted`; write-capable tools require approval.
- Install security scan: `safe`; no warnings.
- Package load: exactly one skill and the expected production MCP server; no loader diagnostics.
- Remote translation: cross-origin redirect header protection is enabled.
- Production authorization-server metadata: HTTP 200, registration endpoint, authorization-code/refresh grants, and S256 PKCE advertised.
- Unauthenticated MCP initialize: HTTP 401 with the protected-resource metadata challenge and FilmEase read/write scopes, as expected.

The package has no Python dependencies, native registration entrypoint, or local executable. Validation called the official admission implementation directly; the full managed-runtime CLI `--install-deps` path was not run locally. Upstream catalog CI must run its normal validation against the pinned public commit.

An interactive Hermes browser OAuth sign-in and authenticated movie tool calls have **not** been completed for this package. The README explicitly documents the native `auth: oauth` server override required by the portable format. Do not treat manifest validation or OAuth discovery as an authenticated end-to-end test.
