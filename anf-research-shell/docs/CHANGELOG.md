# CHANGELOG — ANF Research Shell

## Reproducibility & Enterprise Infra Note (2026-09-07, after v1.6.1)
No new commands/versions — reproducibility infrastructure:
- `make verify` / `make test` → **3 gates** (syntax `zsh -n`, 25 deterministic unit tests, launcher smoke) — the same as the current `make verify` content; green in GitHub Actions.
- `make setup` + aggregate pinned `requirements.txt` (xgboost 3.4.1, scikit-learn 1.9.0, psutil 7.2.2, scapy 2.7.0, torch 2.13.0+cpu [CPU index], scipy 1.18.0, numpy 2.5.2, matplotlib 3.11.1).
- GitHub Actions CI: `.github/workflows/ci.yml` (push/PR → make verify; requires the repo on GitHub).
- `LICENSE` MIT (code) set.
- Experiment scaffold (`anf exp`) produces `seed.txt`/`env.txt`/`run.sh` per run.
- Actual verification: 25/25 tests passed (deterministic, seed 42).

## v1.6.1 (Cyber split from ANF — user decision 2026-09-07)
**Theme: a clean ANF focused on the research workbench. The operational cyber module was moved to a separate archive, not deleted.**

### Changes
- **53 operational cyber functions moved** from `code/anf_research_shell.zsh` to
  `~/Documents/Archive/ANF-cyber-modules-v1.6/anf_cyber_ops_*` (verbatim, still available):
  Recon (`scan`, `subdomains`, `dns`, `whois`, `amass`, `nikto`, etc.), C2 framework
  (`c2-start`, `c2-stop`, `c2-payload`, `c2-exfil`, etc.), OSINT/exploit/defense
  (`osint-email`, `autopwn`, `suricata`, `priv-esc`, `persist`, `honeypot`, etc.),
  IP/targets ops, `matrix` (MITRE mapping).
- **Core script 119 → 64 functions** (3,085 → 2,414 lines).
- **Root cleanup everywhere**: 46 `_ANF_CMD_DESC` entries, `/cmd` aliases, Core help
  (`targets` removed), selftest (`check-ip`, nmap/subfinder tools → git/dig),
  `doctor` tool list (cyber tools dropped), section comments.
- **Research methods retained**: `c2-entropy`, `c2-beacon-math`, `c2-ml-hunt`,
  `c2-zscore`, `eval-*`, `guardfall-metrics`, `benchmark-guardfall` — material for
  test_suite & papers, not promoted as cyber.
- Full backup: `~/Documents/Archive/ANF-cyber-modules-v1.6/anf_research_shell_v1.6.0_FULL_local.zsh`
  (as of the evening of 2026-09-07: the archive is stored LOCALLY outside the repo so the public repo stays free of cyber modules).

### v1.6.1 Verification (all via the `bin/anf` launcher)
- `zsh -n` → SYNTAX OK (final file 2,414 lines)
- `test_suite.py` → 25/25 pass
- `bin/anf hash abc` → MD5 `90015098...` (deterministic)
- `bin/anf mlbench 200` → entropy F1 1.0000 (valid output)
- `anf_selftest` → PASS 15, FAIL 0
- help → 6 domains, 0 cyber residue

---

## v1.6.0 (Research Focus — cyber removed from promotion, 2026-09-06)
**Theme: converge & be functional. ANF = research workbench (science/math/CS), not a cyber tool.**

### Changes (responding to the user: "just remove the cyber so it focuses on other things")
- **Help 20 categories → 6 large domains**: Core, Utilities, Research & Math, ML/DL/Eval, Workbench & AI, Auto-Provisioning. Cyber categories (Recon, Exploitation, OSINT, C2 Framework, Defense, Post-Exploitation, Network&OPSEC) removed from promotion.
- **Banner** without the Cyber/Purple-Team line → Science&Math / ML-DL-Research / Researcher tools / One-terminal.
- **`/why`** without the cyber line; positioning: science/math/computer-science researchers.
- **README** positioning aligned (without cyber).
- **Conservative**: cyber functions were NOT deleted — they can still be called manually (e.g. `anf matrix`) for historical purposes; they are simply not promoted.

### v1.6.0 Verification (ad-hoc, mktemp)
- `zsh -n` → SYNTAX OK
- help → 6 domains shown; 0 cyber categories left in help ✅
- banner & `/why` → do not contain "cyber" ✅
- Regression: test_suite 25/25, calc, sym, csv friendly error handling, ensure ✅
- Conservative: `anf matrix` can still be called ✅

---

## v1.5.1 (Positioning Refocus — Integrated & Reproducible Research Workbench, 2026-09-06)
**Theme: tidy up & focus on one strong story, not 5 half-hearted pillars.**

### Changes (responding to the user's direction: "trim & refocus, single positioning")
- **Single positioning**: "Integrated & Reproducible Research Workbench" — all the researcher's work (science, math, CS, cyber) in one reproducible terminal. Replaces the fragmented multi-pillar banner/why.
- **Version aligned**: `ANF_VERSION` 1.0.0 → **1.5.0** (previously the code said 1.0.0 although features were already v1.5 — inconsistency fixed).
- **Banner** refocused: 4 coherent lines (Science&Math / ML-DL-Research / Cyber-PurpleTeam / One-terminal + /ensure), not a random feature collage.
- **`/why`** = positioning summary + command mapping per domain + the promise "measurable & traceable".
- **README & manifest** aligned to one story.

### v1.5.1 Verification (ad-hoc, mktemp)
- `zsh -n` → SYNTAX OK; `anf version` → 1.5.0
- Banner → v1.5.0 + "Integrated & Reproducible Research Workbench" ✅
- `/why` → headline + Positioning ✅
- Regression: test_suite 25/25, encode hello, calc, sym, mlinfo, ensure, matrix ✅
- (Note: the 1 "FAIL" on the first run turned out to be a test bug — case-sensitive grep "positioning" vs "Positioning"; the code was correct.)

---

## v1.5.0 (Researcher Workflow + Auto-Provisioning, 2026-09-06)
**Theme: every researcher need handled via the terminal + tools installed automatically.**

### New features (Section 15 — Researcher Workflow; Section 16 — Auto-Provisioning)
| Command | Function | Domain |
|---|---|---|
| `/plot` | Data visualization (hist/scatter/line) → PNG to research/outputs (matplotlib) | Science |
| `/calc` | Scientific calculator (sqrt, trigonometry, powers) | Math |
| `/sym` | sympy symbolic mathematics: integrate/diff/simplify/limit/solve/series | Math |
| `/sha` | sha256 checksum for the reproducibility manifest | Research |
| `/exp` | Reproducible experiment scaffold: folder + seed.txt + env.txt + run.sh | Research |
| `/csv` | CSV data wrangling via pandas: head/summary/cols | Science |
| `/bib` | .bib bibliography validation: count/duplicates/type/suspicious lines | Research/writing |
| `/ensure` | **Auto-provisioning**: check required tools + auto-install missing ones (Linux apt/pacman/dnf; brew fallback) | DevOps |

### v1.5.0 Verification (ad-hoc, mktemp)
- `zsh -n` → SYNTAX OK; total functions **119**; 3081 lines
- `test_suite.py` → **25/25**; aliases 8/8; encode OK
- `/calc` → sqrt(144)=12, sin(pi/6)=0.5
- `/sym` → integrate=x³/3, simplify=x+1, limit=1, solve=[-2,2], diff=exp(x)(sin x+cos x)
- `/sha` → 64 hex; `/exp` → 3 scaffold files; `/csv` → rows=3; `/bib` → 2 entries
- `/ensure doctor` → detects apt + missing tools (dry, without installing)
- `/plot` → PNG to research/outputs

### Bug fixes within the iteration
- Merge glitch: `unset _cmd _func# ===` (missing newline between sections) → fixed
- sympy: `sp.sstr(r, pretty_print=False)` → `sp.sstr(r)` (new API); inject sin/cos/exp/log/limit into the namespace
- `/ensure` display: `, wait:wajib` → `[wajib]`

### Auto-install note (answering the user's question)
**Yes.** `/ensure doctor` checks; `/ensure all` auto-installs via the Linux package manager (apt/dnf/pacman) with sudo confirmation. Safe because: (1) doctor mode is dry by default, (2) always confirms before installing, (3) Linux first for stability.

---

## v1.4.0 (Researcher Workflow — batch 1) — merged into v1.5.0 above (single commit)
*(the v1.4 marker is kept in git history; the changelog is summarized in v1.5.0.)*

## v1.3.0 (Research Suite — for science/math/CS/cyber researchers, 2026-09-06)
**Theme: make ALL the researcher's work easier in one terminal — research, math, ML, DL, cyber.**

### New features (Section 14 — Research Suite)
| Command | Function | Domain |
|---|---|---|
| `/mlinfo` | Check the ML/DL stack: python/numpy/sklearn/torch + CUDA, honest CPU flag | ML/DL |
| `/stat` | Numeric dataset statistics: n/min/max/mean/median/std/entropy | Math |
| `/mlbench` | Deterministic benchmark (seed=42): entropy-baseline vs IsolationForest → CSV to research/outputs | ML/research |
| `/dlcheck` | DL CPU smoke test: matmul (quick) / 3-epoch mini-MLP (full), deterministic | DL |
| `/matrix` | Map ANF commands to MITRE ATT&CK tactics (+ filter per tactic) | Cyber |
| `/dataset` | Research dataset registry (add/list, persist to `~/.cache/anf/datasets.tsv`) | Research |
| `/paperstat` | Manuscript statistics (words/lines/refs/size) | Research |

### v1.3.0 Verification (ad-hoc, mktemp, exit 0)
- `zsh -n` → SYNTAX OK; total functions **111**
- `test_suite.py` → **25/25** (regression)
- Aliases 7/7, help category **Research Suite** shown
- `/mlinfo` → sklearn 1.9.0, torch 2.13.0+cu130 cuda=False (honestly CPU-only)
- `/stat` → mean=3, std=1.58114, entropy=2.32 bits (data 1..5)
- `/mlbench 400` → entropy-baseline F1=1.0, isolation-forest F1=0.205; deterministic CSV to research/outputs
- `/dlcheck` quick → matmul shape 4x4; full → 3 epochs, loss decreasing, OK
- `/matrix` → C2/Recon/Defense tactics + reconnaissance filter
- `/dataset` add+list persist; TSV header exactly once
- `/paperstat` → manuscript words/lines

### Bug fixes within the iteration
- `ANF_RESEARCH_DIR: parameter not set` → default `${ANF_RESEARCH_DIR:-$HOME/.../research/outputs}`
- **Critical zsh PITFALL:** `local path=...` inside a function overrides `$PATH` (zsh tie array `path`↔PATH) → all commands not found (`dirname`/`mkdir`/`mv`/`date`). Fix: rename to `pth`.
- `/dataset` double header → write the header only if the file is new.

---

## v1.2.1 (Focus Value Proposition, 2026-09-06)
**Theme: sharpen the identity — not a rival shell, but a security research cockpit.**

### Changes
- `/why` command — concise value proposition (3 pillars: reproducible research, purple-team ops, AI-assisted).
- Identity-focused banner: "Security-Research Cockpit │ Reproducible │ AI-assisted │ Purple-Team".
- README positioning written explicitly.

### v1.2.1 Verification
- `/why` → tagline + 3 pillars shown; alias `/why`→`anf_why`; Enterprise help contains why.
- Baseline regression still green (syntax, 25/25, encode).

---

## v1.2.0 (Enterprise Features — 6 high-impact commands, 2026-09-06)
**Theme: add balanced functionality across domains to help all work.**

### New features (Section 13 — Enterprise)
| Command | Function | Domain |
|---|---|---|
| `/gen` | AI-assisted code generation (calls hermes/opencode, without AI → clear message) | Coding |
| `/task` | Task orchestrator in **background tmux** — survives restart; `start/list/attach/kill` | Automation/research |
| `/q` | Quick grep search within the project (skips .git/node_modules/venv) | Productivity |
| `/gitx` | Daily git quick-ops: `s/a/c/l/b/p/d/x` | Git |
| `/envcard` | Env & tools summary at a glance (OS/uptime/disk/mem/tools) | Monitoring |
| `/mvp` | Multi-version & branch mgmt: `tag/v/b/s/l` | Git/versioning |

### v1.2.0 Verification (ad-hoc, mktemp)
- `zsh -n` → SYNTAX OK
- `test_suite.py` → **25/25** (baseline regression green)
- Aliases `/gen /task /q /gitx /envcard /mvp` → all registered ✅
- `/envcard` → OS/Disk/Mem sections + tools (git/tmux/docker/python3/node/hermes/opencode) ✅
- `/q` → grep returns matches ✅
- `/gitx s` → git status ✅
- `/task` tmux lifecycle: start+list+kill ✅ (survive restart confirmed)
- `/mvp v` → `ANF_VERSION=1.0.0` ✅
- `help` → **Enterprise** category shown ✅
- Total functions: 97 → **103**

### Footnotes
- The 6 commands were chosen from user preference (balanced, high impact, not 20-odd).
- `gen`&`task`&`gitx`&`q`&`envcard`&`mvp` are also available via the NON-interactive `anf <cmd>` launcher.

---

## v1.0.0 (Enterprise + Research Rebrand, 2026-09-06)
**Theme: ANF → ANF Research Shell. Enterprise structure + research foundation.**

### Main changes
- **Total rebrand:** all `anf`/`ANF`/`_af_`/`_AF_` identifiers → `anf`/`ANF`/`_anf_`/`_ANF_` (manual, verified 0 leftover). Version reset to `1.0.0`.
- **New enterprise/research structure** at `~/Documents/ANF-Research-Shell/`:
  - `code/` — core script + tests + `anfrc.dist` (config template)
  - `bin/anf` — **CLI launcher**: `anf <cmd> [args]` (single command) or `anf` (interactive shell)
  - `research/` — experiment output (runtime, not committed)
  - `docs/`, `server/`, `packages/`
  - `install.sh` — enterprise installer (installs launcher + core script + config)
- **`bin/anf` launcher** with layered location resolution (env `ANF_LIB` → install location → project) → portable/self-contained.
- **Config** `anfrc.dist` (prefix `ANF_`), read at launch.
- **Academic README** + a re-verifiable baseline.

### v1.0.0 Verification
- `zsh -n code/anf_research_shell.zsh` → **SYNTAX OK**
- `python3 code/test_suite.py` → **25/25 (100%)**
- `bin/anf encode hello` → `aGVsbG8=` (base64) ✅
- `bin/anf hash abc` → MD5 `90015098...`, SHA256 `ba7816bf...` ✅
- `bin/anf version` / `status` / `help` → run from the launcher ✅
- Post-source aliases: `/help`→`anf_help`, `/status`→`anf_status`, `/c2-start`→`anf_c2_start` ✅
- Portable install to `/tmp/anf-install-test/` → `anf version` runs (self-contained) ✅
- `.zshrc` updated → `source ~/Documents/ANF-Research-Shell/code/anf_research_shell.zsh`

### Footnote
- The old `.../zsh project/` folder is retained (v15 + archive traces). Not deleted.
- Cache dir automatically moves to `~/.cache/anf/` (var renamed `ANF_CACHE_DIR`).
- TODO: git init + tag, license, CI, paper §5 experiment.

---

## v15.0.1-RESEARCH (Hotfix /help + popup) — pre-rebrand history

### Bug Fixes (2026-09-06, after a laptop restart)

| ID | Location | Problem | Fix |
|----|--------|---------|-----|
| FIX-ALIAS-ORDER | Section 10, `/cmd` alias loop (line ~1973) | The alias-creating loop ran **before** `anf_help` was defined (line ~1980) → the `/help` alias was never created; `/help` failed with `zsh: no such file or directory: /help` even though `/status` etc. worked | Move the alias loop to **after** the `anf_help` function (before Section 11). Now all 97 command functions exist when aliases are created |
| FIX-POPUP-NL | Section 11, `_af_popup_render` | The header line `' matches ─╮\n'` used plain single quotes → `\n` was displayed **literally** (not as a newline); likewise `out+="│ ...\\n"` in double quotes → literal `\n` | Switch to `$'...'` (ANSI-C quoting) in the header and `"...\"$'\n'` on each match line → real newlines |

### v15.0.1 Verification
- `zsh -n anf_v15.zsh` → **SYNTAX_OK**
- Sourced in a subshell → aliases `/help -> anf_help`, `/status -> anf_status`, `/scan -> anf_scan` ✅
- Popup render unit check (`cat -A`): no literal `\n`, real newlines on each line ✅
- `test_suite.py` → **25/25 passed (100%)**

---

## v15.0.0-RESEARCH (Merge v12.8.7-FINAL → v14.0.0-RESEARCH)
**Theme: Unification — v14 Base (research/hardening) + v12 Operational Features**

### Merge Strategy (2026-09-06)
- **Base = v14.0.0-RESEARCH** (all hardening retained):
  - v14 C2 server (subprocess.run + shlex + sandbox) — NOT overwritten by the v12 server
  - non-destructive `panic` (`unset HISTFILE` + `fc -p /dev/null`) — NOT overwritten
  - `suricata` if/else fix (parse error) — NOT overwritten
  - ZLE popup if/else, hash without eval, eval framework, GuardFall v2 metrics
- **+61 v12 functions** missing in v14 (file `anf_v12.8.7-FINAL.zsh` archived)
- **3 overrides** (v12 richer, last definition wins): `autopwn` (nmap+msf+nuclei), `osint_email` (+hunter.io), `sysinfo` (full laptop report)
- **Skip** v12 bare-name wrappers (`function katana/nuclei/ffuf/...`) — binary shadowing risk

### New Features from v12 (61 functions)
| Category | Functions |
|----------|--------|
| IP cache | `check-ip`, `clear-ip`, `cached_ip` |
| Targets | `targets add/list/remove/clear` |
| Config | `config`, `paths`, `version`, `install`, `update`, `log`, `selftest` |
| Recon | `dns`, `whois`, `quickrecon`, `subdomains-json` |
| Wireless | `wifisurvey`, `monitor`, `mac`, `pilgrims` |
| Network | `screenshot`, `proxy` |
| Utils | `uuid`, `random`, `epoch`, `clip` |
| Tools | `amass`, `gobuster`, `nikto`, `whatweb` |
| Integrations | `hermes`, `hermes-locate`, `hermes-set`, `github` |
| C2 ops | `c2-payload`, `c2-interact`, `c2-exfil`, `c2-decode` (v14 server retained) |
| OSINT | `osint-phone`, `cloud-enum`, `wayback` |
| Exploit | `priv-esc`, `password-spray` |
| Pivot | `pivot-socks`, `port-forward`, `ssh-tunnel` |
| Defense | `honeypot`, `wazuh-agent` |
| Post-exploit | `persist`, `credentials`, `screenshot-all`, `clear-logs` |
| Others | `playbook`, `ai-assist`, `ai-log` |

### v15 Verification
- `zsh -n anf_v15.zsh` → SYNTAX_OK
- Source OK — `ANF_VERSION=15.0.0-RESEARCH`
- `test_suite.py` → 25/25 (still green, untouched)
- Selftest → **16/16 PASS** (including the check-ip network test)
- Duplicate functions: only the 3 intentional overrides (autopwn/osint_email/sysinfo); no unintentional duplicates; `function katana/nuclei/...` = 0
- Help with 16 categories shows the new commands

---

## v14.1.0-RESEARCH (Audit)
**Theme: Bug Fixes + Baseline Verification**

### Bug Fixes (Audit 2026-09-06)

| ID | Location | Problem | Fix |
|----|--------|---------|-----|
| FIX-PARSE | Section 12, `anf_suricata` | `command -v ... && sudo suricata ... & \` then `\|\|` → **zsh parse error** (`zsh -n` failed at line 1300), the script could not be sourced | Refactor to `if command -v; then ... & else ...; fi` — background only on the command, the `\|\|` chain split out |
| FIX-REPRO | `test_suite.py` Isolation Forest | Claimed "seed=42 produces the same values" but the two runs used different initial random states → different F1 (0.75 vs 0.70), **not a valid reproducibility test** | Replace with a correct invariant test: two runs with `random.seed(42)` reset before each `fit()` → predictions must be identical (`preds_r1 == preds_r2`) |

### Baseline Verification
- `zsh -n anf_v14.zsh` → **SYNTAX_OK** (before the fix = parse error line 1300)
- `source` the script in a subshell → OK, `/help` exit 0
- `test_suite.py` → **25/25 passed (100%)** (previously 24/24 — +1 new reproducibility test)
- Runtime smoke test: `encode`/`decode`/`hash`/`status` all correct (MD5 abc, SHA256 ba7816...)
- Consistency audit: 36 public functions ↔ 34 slash commands, all mappings valid; `panic` non-destructive (unset HISTFILE), `c2_start` subprocess.run+shlex+sandbox, ZLE popup if/else correct

---

## v14.0.0-RESEARCH (Current)

### Bug Fixes (Critical)

| ID | Location | Problem v13 | Fix v14 |
|----|--------|-------------|---------|
| FIX-C2 | Section 6, `server.py` | `os.popen(cmd).read()` — shell injection, no timeout | `subprocess.run()` + `shlex.split()` + timeout=15s + sandbox CWD |
| FIX-GF | Section 3, `_af_canonicalize_command` | `${IFS}` variant not detected | Add `${IFS}`, `${IFS:-}`, tab char to the IFS check |
| FIX-PANIC | Section 5, `anf_panic` | `rm -f ~/.zsh_history` — destructive & irreversible | Replace with `unset HISTFILE` + `fc -p /dev/null` only |
| FIX-ZLE | Section 11, `_af_ki_accept` | `BUFFER=[[ ... ]] && ... \|\| ...` — zsh syntax error | Use a proper `if/else` |

### New Features

#### GuardFall v2 [NEW-GF, NEW-GF-M, NEW-BENCH]
- **Unicode normalization**: Remove full-width chars (ｒｍ→rm), zero-width chars (U+200B), null bytes
- **Expanded patterns**: env injection (`LD_PRELOAD`), path traversal (`../../../`), fork bomb (`:(){:|:&};:`)
- **Metrics tracking**: TP, FP, FN, TN → Precision, Recall, F1, Accuracy (per session)
- **`/guardfall-metrics`**: Display & export JSON
- **`/benchmark-guardfall`**: Labeled dataset (12 ATTACK + 8 BENIGN) with quantitative evaluation

#### Math/ML Enhanced [NEW-ENT, NEW-BEACON, NEW-ISO]
- **Entropy Bootstrap CI**: 95% Confidence Interval via 500 resamples
- **Empirical threshold**: Validated via ROC sweep (optimal H≈6.0, F1=1.0 on 400 samples)
- **Beaconing Chi-Square**: Test H₀ of interval uniformity — H₀ rejected → non-uniform → beaconing
- **Beaconing Autocorrelation R(1)**: High periodicity → R(1) → 1.0
- **Isolation Forest**: Pure Python implementation (without scipy/sklearn) — Liu et al. (2008)
- **Z-Score retained** as the comparison baseline for the paper

#### Evaluation Framework [EVAL-01, EVAL-02]
- **`/eval-entropy-roc`**: ROC sweep threshold 3.0–8.0, CSV output
- **`/eval-compare-ml`**: Z-Score vs Isolation Forest with export to file
- **`/eval-export-audit`**: Audit log JSONL → CSV (pandas-ready)

#### C2 Improvements [NEW-C2LOG]
- **JSONL session logging**: Timestamp, client IP, command, blocked status, output length
- **`/c2-sessions`**: Display the log with `jq` or plaintext
- **SIGTERM graceful shutdown**: Server shuts down cleanly via `kill -TERM`
- **Minimal ENV**: `PATH=/usr/bin:/bin:/usr/local/bin` only — prevents env injection

#### Categorized Help [NEW-HELP]
- 9 categories: Core, Recon, OPSEC, C2 Framework, Math/ML, Security Engine, Evaluation, Defense, Output

### Added Commands
```
/c2-sessions         # View the C2 session log
/c2-zscore           # Z-Score baseline (for paper comparison)
/guardfall-metrics   # GuardFall TP/FP/F1
/eval-entropy-roc    # ROC CSV export
/eval-compare-ml     # ML comparison
/eval-export-audit   # Audit → CSV
```

### Test Suite
- `test_suite.py`: 24 unit tests, 6 modules
- 100% pass rate (24/24) — verified before release

---

## v13.0.0-ULTIMATE (Previous)
- C2 framework: `os.popen()` (vulnerable)
- GuardFall: basic patterns, no metrics
- Entropy: hardcoded threshold without empirical justification
- Beaconing: jitter only, no statistical test
- ML: Z-Score only
- No evaluation framework
