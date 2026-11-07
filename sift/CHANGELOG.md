# Changelog — sift

## v1.0.0 — 2026-09-06
First public release (go1.21+, pure stdlib).

### Features
- Hardcoded secret detection: 5 default patterns + 2 custom rules (including `sk-proj-*`)
- Shannon entropy analysis for unknown/obfuscated secrets
- Git-aware mode (`--git-diff`) — changed files only
- Worker-pool file scanning (concurrent)
- Pretty/JSON output, no telemetry (zero network)

### Technical
- Static Linux/macOS/Windows binary (<2.5MB), zero runtime dependencies.
