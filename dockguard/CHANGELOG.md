# Changelog — dockguard

## v1.0.0 — 2026-09-06
First public release (Python stdlib).

### Features
- 10 Dockerfile lint rules (USER, HEALTHCHECK, base image tag, etc.)
- CLI: `--format json|github|pretty`, `--quiet`, `--ignore <rules>`
- Exit code: 0 clean / 1 findings present / 2 file not found
- GitHub Actions-compatible output
