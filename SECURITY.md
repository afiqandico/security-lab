# Security Policy

> Responsible disclosure keeps everyone safe.

## Supported Versions

| Version / Branch | Supported |
|---|---|
| main (latest) | Yes |
| Older tagged releases | Best-effort only |

## Reporting a Vulnerability

Do not open a public GitHub issue for security vulnerabilities. Use
[GitHub Security Advisories](https://github.com/afiqandico/security-lab/security/advisories/new)
for this repository — reports stay private between you and the maintainer.

Please include: description, reproduction steps, affected version/subproject,
potential impact.

## Response Timeline

| Stage | Target |
|---|---|
| Acknowledge report | Within 72 hours |
| Initial assessment | Within 7 days |
| Fix & disclosure | Case-by-case |

## Scope

In scope: vulnerabilities in this repo's code (or any subproject folder for monorepos).
Out of scope: third-party dependency bugs (report upstream), physical access attacks,
social engineering, and issues that only affect intentionally vulnerable
documentation examples or test fixtures.

## Out of scope by design

- Placeholder values in documentation, `runbook.md`, and test fixtures — they are
  deliberate examples, never real credentials (see `.gitleaks.toml`).
- Missing hardening in components explicitly marked as lab-only tools.

Thank you for reporting responsibly.
