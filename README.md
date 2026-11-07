# Security Lab

A collection of CLI tools for **purple-team** practice and security hygiene: secret
scanning, Dockerfile linting, log anonymisation, CI/CD scan orchestration, a
penetration-testing framework, and a cyber-operations workbench. Everything runs
**locally** — no paid services, no telemetry, and some components need only the
Python standard library or the Go standard library.

> ## ⚠️ Ethics and scope of use
> The offensive tools in this repo (`pilgrims`, `ANF-Cyber-Shell`) may be run **only**
> against systems you own or have **written permission** to test. Running scans or
> exploitation without authorisation is a legal violation (in Indonesia: the ITE Law)
> and a breach of professional ethics. No findings from real organisations are
> published in this repo; every target in the documentation is a placeholder
> (`example.com`, `192.168.x.x`, `10.0.0.x`).

---

## What is in this repo

| Component | Language | Function | Prerequisites | Tests |
|---|---|---|---|---|
| [`seclab`](seclab) | bash | Dispatcher: one command for every tool (`seclab list`, `seclab doctor`) | bash | `./seclab doctor` |
| [`sift/`](sift/) | Go | Secret scanner: 5 built-in rules + custom `.siftrules`, Shannon entropy, pretty/JSON output, `--git-diff` mode | Go 1.21+ | 7 unit tests (`go test ./...`) |
| [`dockguard/`](dockguard/) | Python | Dockerfile linter: 10 rules (DG001–DG010), 3 output formats, CI-friendly exit codes | Python 3.8+ (stdlib only) | 47 unit tests |
| [`log-anonymizer/`](log-anonymizer/) | Python | Log anonymisation: hashing, tokenisation, k-anonymity, Laplace DP; CLI + FastAPI endpoint | Python 3.11+, `pip install -r requirements.txt` | 2 unit tests (with deps) |
| [`sec-scan-cli/`](sec-scan-cli/) | Python | Scan orchestration: `npm audit`, `pip-audit`, gitleaks (pinned + SHA-256), Suricata → aggregated report | Python 3, optional external tools | smoke test |
| [`pilgrims/`](pilgrims/) | bash | Testing framework: 20 modules (recon, web, AD, cloud, wireless, red-team, forensics, …) | bash, `nmap`/`curl`/`jq` (certain modules) | `tests/unit/run_all.sh` |
| [`anf-research-shell/`](anf-research-shell/) | zsh | Reproducible research workbench: statistics, ML/DL, plotting, experiment scaffolding (seed/env/run.sh), checksums | zsh ≥ 5.0, Python 3 | `make verify` (25 tests) |
| [`ANF-Cyber-Shell/`](ANF-Cyber-Shell/) | zsh | Cyber-ops workbench: recon/OSINT, hardened C2, C2 detection analytics, defence (Suricata/YARA) | zsh ≥ 5.0, optional tools (`nmap`, `suricata`, `yara`) | `make verify` (10 gates) |
| [`runbook.md`](runbook.md) | — | End-to-end walkthrough of `sift` + `dockguard` with real output | — | — |

Each component remains self-contained (its own README, licence, dependencies);
this repo consolidates distribution and provides a single entry point.

---

## How to run

```bash
git clone https://github.com/afiqandico/security-lab.git
cd security-lab

./seclab list        # list tools
./seclab doctor      # check which runtimes are available on your machine
```

### Per-tool examples

```bash
# 1) Secret scanning (sift, Go)
cd sift && go build -ldflags="-s -w" -o sift . && cd ..
./seclab scan-secret .              # or: ./sift/sift --json . > report.json

# 2) Dockerfile linting (dockguard, Python stdlib)
./seclab lint-docker --format json Dockerfile

# 3) Log anonymisation (log-anonymizer; needs deps)
cd log-anonymizer && pip install -r requirements.txt && cd ..
./seclab anon-log data/sample.csv /tmp/out.csv --algorithm hash --columns ip

# 4) CI scan pipeline (sec-scan-cli)
./seclab scan --help
./seclab ci-scan gitleaks           # run a single scanner

# 5) Workbench (zsh)
./seclab anf encode hello           # → aGVsbG8=
./seclab anf-cyber selftest
```

Minimum prerequisites: `git`, `bash`, `python3`. For specific components: `go`
(sift), `zsh` (ANF shells), and any external tool that is actually invoked (e.g.
`nmap`, `suricata`, `yara`, `gitleaks`) — all optional and detected by
`seclab doctor`/`anf doctor`.

---

## Verification status (2026-11-06)

Run on Linux (kernel 7.0), Go 1.27.1, Python 3.14.9, zsh 5.9:

| Check | Result |
|---|---|
| `cd sift && go build -ldflags="-s -w" -o sift .` | OK — binary ≈3 MB |
| `cd sift && go test ./...` | OK (7 tests) |
| `cd dockguard && python3 -m pytest tests` | 47 tests, all passing |
| `bash ANF-Cyber-Shell/scripts/verify.sh` | PASS 10 / FAIL 0 |
| `cd anf-research-shell && make verify` | `zsh -n` OK, 25/25 tests passing |
| `cd log-anonymizer && pytest -q` | 2 tests passing (venv + deps) |
| `cd sec-scan-cli && pytest -q` | 2 tests passing |
| `./seclab doctor` | all runtimes detected |
| `gitleaks detect --no-git --redact` | **0 leaks** (see [`.gitleaks.toml`](.gitleaks.toml)) |

Honest notes:

- `log-anonymizer` needs third-party dependencies (`pandas`, `click`, `numpy`,
  `scikit-learn`, `cryptography`, `fastapi`); its tests can only run after
  `pip install -r requirements.txt` (tested in a separate venv).
- `sec-scan-cli` invokes external tools; when a tool is missing, the relevant
  scanner is skipped and reported as *skipped*, not as a failure.
- `pilgrims` and `ANF-Cyber-Shell` are **automation wrappers**: results depend on
  the tools installed and on the target being tested.

---

## Layout

```
security-lab/
├── seclab                  # CLI dispatcher
├── runbook.md              # sift + dockguard walkthrough
├── .gitleaks.toml          # narrow allowlist for documentation examples
├── sift/                   # Go  — secret scanner
├── dockguard/              # Py  — Dockerfile linter
├── log-anonymizer/         # Py  — log anonymisation
├── sec-scan-cli/           # Py  — CI/CD scan orchestration
├── pilgrims/               # sh  — 20 testing modules
├── anf-research-shell/     # zsh — research workbench
└── ANF-Cyber-Shell/        # zsh — cyber-ops workbench
```

---

## Ethics, limits, and safety

- **Written permission required.** Recon, scanning, and exploitation only against
  your own assets or those you are authorised in writing to test. This
  documentation does not endorse unauthorised testing.
- **Lab-only.** The C2/exploit/persistence modules in `ANF-Cyber-Shell` and
  `pilgrims` are intended for isolated labs, not production.
- **Not a substitute for expert judgement.** These tools automate repetitive
  checks; security decisions still require a human.
- **No warranty.** MIT, without warranty of any kind (see [`LICENSE`](LICENSE)).
- **Placeholders, not credentials.** Secret values in documentation/tests are fake
  examples. Never commit real credentials; `.gitleaks.toml` is deliberately narrow
  so that new secrets remain detectable.
- **Reporting security issues:** see [`SECURITY.md`](SECURITY.md) (use GitHub
  Security Advisories, not public issues).

## Contributing

See [`CONTRIBUTING.md`](CONTRIBUTING.md). For larger changes, open an issue first
so the direction can be agreed on.

## License

MIT — see [`LICENSE`](LICENSE). Components that carry their own licence continue to
follow the licence in their subfolder.
