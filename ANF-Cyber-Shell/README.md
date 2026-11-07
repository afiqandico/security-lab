# ANF Cyber Shell

**Purple-Team / Cyber-Operations Zsh Workbench — one terminal for C2, recon, OSINT, exploit, defense, and C2 detection, with measurable output.**

*Rebuilt standalone from the ANF v1.6.0 archive (which contained the complete cyber module), after the cyber module was split from ANF Research Shell on 2026-09-07. This project does not depend on external services; all modules run locally.*

ANF Cyber Shell wraps the entire security operations cycle (recon → C2 → detection analytics → defense) into a single deterministic, hardened, and tested zsh CLI, along with the GuardFall v2 engine for command input sanitization.

---

## Why ANF Cyber Shell
One terminal where all **purple-team** work can be run consistently:

- **C2 Framework (hardened v2)** — C2 server uses `subprocess.run()`+`shlex`+sandbox+minimal env; JSONL session log; graceful shutdown.
- **C2 Detection Analytics** — Shannon entropy + bootstrap CI, beaconing (jitter/chi-square/R(1)), Isolation Forest, Z-Score, ROC sweep. Verifiable detection research material.
- **Recon / OSINT** — subdomain enum, nmap scan, amass/gobuster/nikto/whatweb, dns/whois/quickrecon, osint-email/phone, cloud-enum, wayback.
- **Exploit / Post-Exploit / Pivot** — autopwn (nmap+msf+nuclei), priv-esc, password-spray, pivot-socks, port-forward, ssh-tunnel, persist, credentials.
- **Defense / Blue** — suricata IDS, yara scan, honeypot, wazuh-agent, GuardFall v2 metrics (TP/FP/F1), benchmark.
- **OPSEC** — panic (non-destructive shell sanitize), tor toggle, encode/decode/hash, check-ip, proxy.

Run `anf-cyber help` for the command list per category; `anf-cyber selftest` to check the baseline; `make verify` for the verification gate.

---

## Installation
```bash
./install.sh                 # install to /usr/local/bin + config + lib
# or without root:
./install.sh --prefix "$HOME/.local"
```

## Usage — a single `anf` entry point
After setup (see below), all ANF shells are accessed through **one menu** `anf`:
```bash
anf               # menu banner (Research / Cyber)
anf research      # enter ANF Research Shell (ML/math/reproducible) — anf-research launcher
anf cyber         # enter ANF Cyber Shell (purple-team/ops)        — anf-cyber launcher
# anf <cmd>...    # (legacy) dispatch command to Research Shell
```

### Cyber Shell directly (`anf-cyber`)
```bash
anf-cyber                # interactive shell (ZLE popup + banner)
anf-cyber help           # command list per category
anf-cyber set-target example.com
anf-cyber scan           # nmap -sV -sC
anf-cyber subdomains example.com
anf-cyber c2-start       # start C2 server (hardened)
anf-cyber c2-entropy     # Shannon entropy + CI
anf-cyber encode hello   # base64
anf-cyber hash abc       # MD5/SHA256/SHA512
```
Inside the shell: `/scan`, `/subdomains`, `/c2-start`, `/c2-sessions`, `/osint-email`, `/guardfall-metrics`, etc. (alias `/cmd`).

### Unified launcher setup (Research + Cyber)
```bash
# Cyber shell (this repo)
./install.sh --prefix "$HOME/.local"          # bin/anf-cyber
install -m755 bin/anf "$HOME/.local/bin/anf"  # unified entry-point menu

# Research shell (FROZEN repository, NOT modified)
~/Documents/Projects/ANF-Research-Shell/install.sh --prefix "$HOME/.local"
install -m755 ~/Documents/Projects/ANF-Research-Shell/bin/anf "$HOME/.local/bin/anf-research"
```
`.zshrc` does not need to source the core script — just `export PATH="$HOME/.local/bin:$PATH"`.

## Verification (baseline gate)
```bash
make verify   # alias: make test
```
Runs the safe gate: `zsh -n` (parse), consistent versions, complete `/cmd`→function mapping, core command smoke test, and launcher. All without executing dangerous commands. For the latest status: see `docs/CHANGELOG.md`.

---

## ⚠️ Disclaimer
Purple-team / detection research **only for authorized environments** (own lab, CTF, official scope). Misuse violates the law. See `NOTICE.md`.
