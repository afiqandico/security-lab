# ANF Research Shell

[![CI](../../actions/workflows/anf-research-shell-ci.yml/badge.svg)](../../actions/workflows/anf-research-shell-ci.yml)

**Integrated & Reproducible Research Workbench — one terminal for the researcher's work (science, math, CS), with measurable and traceable output.**
*v1.0.0 = enterprise/research structure; v1.5.0+ = research workbench focus.*

ANF wraps scientific computation, ML/DL, visualization, experiment scaffolding, and reproducibility verification into a single deterministic and tested CLI.

---

## Why ANF (positioning — one story)
**ANF = Integrated & Reproducible Research Workbench.** Not just a shell, but one terminal where all the researcher's work (science, math, computer science) can be run **consistently, measurably, and traceably**:

- **Science & Math** — statistics, calculator, symbolic mathematics, visualization
- **ML / DL / Eval** — stack-check, deterministic benchmark, DL smoke test, experiment scaffold, data wrangling, bibliography
- **Reproducible** — deterministic seed, 25-test suite, experiment scaffold (`seed.txt`/`env.txt`/`run.sh`) per run
- **Self-contained / Tidy** — auto-install Linux tools (`/ensure`), installer, git, docs

Run `anf why` for a brief summary and `anf help` for the command list.

---

## Installation
```bash
./install.sh                 # install to /usr/local/bin + config + lib
# or without root:
./install.sh --prefix "$HOME/.local"
```

## Usage
```bash
anf                # interactive shell (ZLE popup + banner)
anf help           # list all commands
anf encode hello   # aGVsbG8=
anf hash abc       # MD5/SHA256/SHA512
anf status         # session status
anf envcard        # system & tools summary
anf task start demo "python3 train.py"   # background tmux task (survives restart)
anf task list      # task list
anf q "pattern" .  # quick grep search
anf gitx s         # concise git status
anf gen "buat script backup"   # AI-assisted code gen (hermes/opencode)
```

Inside the shell: `/gen`, `/task`, `/q`, `/gitx`, `/envcard`, `/mvp` (alias `/cmd`).

## Research Suite — for researchers (science, math, computer science)
All research work in one terminal:
```bash
anf mlinfo                    # ML/DL stack: torch/sklearn/numpy/CUDA
anf stat data.txt             # dataset statistics: mean/median/std/entropy
anf mlbench 400               # deterministic benchmark → research/outputs/*.csv
anf dlcheck                   # DL CPU smoke test (torch, seed=42)
anf dlcheck full              # synthetic 3-epoch mini-MLP
anf paperstat manuscript.md   # manuscript word/line/ref statistics
```
Historical functions (e.g. `anf matrix` / MITRE ATT&CK, `c2-*`) remain available for compatibility
but have not been promoted since v1.6.0 — see CHANGELOG.
Benchmark results are written automatically to `research/outputs/` — numeric evidence for reproducibility.

## Researcher Workflow (all via terminal)
```bash
anf plot data.csv 2 hist "judul"   # visualization -> research/outputs/*.png
anf calc "sqrt(144)"               # scientific calculator
anf sym "integrate(x**2, x)"       # symbolic mathematics (sympy)
anf sha manuscript.tex             # reproducibility checksum
anf exp nama_eksperimen            # scaffold: folder+seed+env+run.sh
anf csv summary data.csv           # data wrangling (pandas)
anf bib references.bib             # bibliography validation
```

## Auto-Provisioning (Linux)
Tools that are not yet installed are installed automatically:
```bash
anf ensure doctor   # check required tools (dry, safe)
anf ensure all      # auto-install what is missing (confirms first, needs sudo)
```
Supports apt (Debian/Ubuntu), dnf (Fedora), pacman (Arch), brew (fallback). **Linux first** for stability.

## Verification (reproducibility)
```bash
zsh -n code/anf_research_shell.zsh        # MUST: SYNTAX OK
python3 code/test_suite.py                # MUST: 25/25 pass
bin/anf hash abc                          # MD5 90015098...
```

## Verification & reproducibility
```bash
make verify    # zsh -n + 25 deterministic tests + launcher smoke test
make setup     # optional: .venv + requirements.txt (for ML/DL commands)
```

`make verify` only requires **zsh + Python 3 stdlib** — no external dataset,
no venv, no network. Its gates: core script syntax, 25 unit tests
(entropy, ROC, GuardFall, beaconing, C2 math), then launcher smoke (`encode`, `hash`).

Experiment artifacts (`research/outputs/*.csv|*.png`) are produced by `anf mlbench`,
`anf plot`, and `anf exp`; result files are not committed (see `.gitignore`).
Authorship & AI disclosure: see `NOTICE.md`.

---

## Structure
```
anf-research-shell/
├── bin/anf                  # CLI launcher
├── Makefile                 # make setup / verify / test
├── requirements.txt         # optional pinned dependencies (ML/DL commands)
├── LICENSE                  # MIT (code)
├── NOTICE.md                # authorship + AI disclosure + standalone statement
├── .github/workflows/ci.yml # CI: make verify on push/PR
├── code/anf_research_shell.zsh  # core script (64 functions, research focus; cyber module archived separately)
├── code/test_suite.py       # 25 deterministic tests
├── code/anfrc.dist          # configuration template
├── docs/CHANGELOG.md        # version history
└── install.sh               # installer
```

## Contribution & ethics
Tools for **security research and education** (lab/own environment). Not for illegal activity. Use outside your own environment is the user's responsibility.

## AI disclosure
The tool was developed & documented with the help of AI (Hermes, model DeepSeek V4 Flash). Lead author: **Afiq Andico Pangimpian** (Independent Researcher).

## License
Code: **MIT** (see `LICENSE`, 2026).
