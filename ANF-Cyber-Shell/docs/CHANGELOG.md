# CHANGELOG — ANF Cyber Shell

## v1.0.4-cyber (Controlled passthrough, aliases do not shadow binaries, opt-in UX — 2026-09-13)

**Theme: the shell stops "taking over" the user's terminal, and provides a single safe entry point for running external commands.**

### Changes
| # | Change | Reason |
|---|-----------|--------|
| 1 | New command `anf run -- <cmd> [args]` (function `anf_run`) | The only path for executing external commands from inside ANF; implemented as `"$@"` without `eval`, so arguments are not re-parsed and ANF aliases are not invoked (`anf run -- stat` remains `/usr/bin/stat`). Every call is logged to `$AF_LOG_FILE`; the exit code is propagated. |
| 2 | Alias loop no longer shadows real binaries | When an ANF command name already exists in `PATH` (`stat`, `install`, `proxy`, `whois`, `nikto`, `gobuster`, `whatweb`, `hermes`, …), the alias is skipped and that name is recorded in `_ANF_SHADOWED`; the ANF command remains available as `anf <cmd>`. `anf config` displays the skipped list. |
| 3 | UX block (`bat`/`ls`/`grep`/`cd`/`less`/`fd`/`log` replacement) is now **opt-in** (`ANF_UX_ALIASES=1`) | The default shell behaves like a normal zsh; no clashes with the user's habits or other tools. |
| 4 | `c2-decode` writes to `mktemp "${TMPDIR:-/tmp}/anf-exfil-XXXXXX.bin"` | Previously the path `/tmp/exfil_<HHMMSS>.bin` could collide/be guessed; it is now a unique file with secure permissions from `mktemp`. |
| 5 | `/`-prefixed aliases removed from 3 sections | `zsh` treats `/cmd` as a path — those aliases were never active and only caused confusion. Plain-name aliases are still created by a single loop at the end of the file. |

### v1.0.4-cyber Verification
- `zsh -n code/anf_cyber_shell.zsh` → OK
- `bash scripts/verify.sh` → PASS=10 FAIL=0 (the version gate now compares against the top CHANGELOG entry, not a hardcoded string)

## v1.0.1-cyber (Bugfix: aligned boxes + alias-resistant binary lookup — 2026-09-10)

**Theme: two bugs that surfaced in daily use — the banner/help border was off and `hermes` failed to be invoked from inside the shell.**

### Fix
| # | Symptom | Root cause | Fix |
|---|--------|--------------|-----------|
| 1 | Banner border & `help` title skewed (title line wider/misaligned from the border); in the banner the right border was missing entirely | The `═══` border was hardcoded assuming a fixed title length, whereas the title contains `${ANF_VERSION}` | Helper `_anf_box <color> "title" ["line-2" ...]` — border width is computed from the content. Used in **7 places**: banner, `help` header, and 5 analytics headers (GuardFall, Shannon, Beaconing, Isolation Forest, ML compare) |
| 2 | `hermes` / `/hermes` from inside ANF → `command not found: alias hermes=anf_hermes` | Every ANF command is registered as a **plain-name alias**, so `command -v hermes` returns the ALIAS TEXT (not a path); that text was stored in `HERMES_BIN` and then executed | Helper `_anf_which` (uses `whence -p`, scans PATH only) + `_anf_has_bin`; used in `anf_hermes_locate`, `anf_gen`, `anf_doctor`, and the tool wrapper |

### The same bug class (all `command -v <command-name>` sites) is also fixed
`amass`, `gobuster`, `nikto`, `whatweb`, `suricata`, `whois`, and the `anf_doctor` tool loop.
Previously the tool check **always passed** (false-positive) even when the tool was not installed → the user received "command not found" instead of a friendly message.

### Additional fix (found by the gate during this release)
| # | Symptom | Root cause | Fix |
|---|--------|--------------|-----------|
| 3 | `ensure` appeared in `help` & `_ANF_CMD_DESC`, but `ensure`/`anf ensure` → `command tidak dikenal` | During the research→cyber merge, the `ensure` entry came along but the `anf_ensure` definition was left behind in ANF Research Shell → gate #4 (`/cmd`→function mapping) had been **RED since the merge**, and the v1.0.0 manifest wrongly claimed the mapping was "intact" | `anf_ensure` was ported from `ANF-Research-Shell` (using `_anf_has_bin`; the `[wajib]` label is now only for the `urgent=1` flag — the research version displayed it for "0" too) | `make verify` is now **PASS=10 FAIL=0** |

Honesty note: this red gate #4 is **pre-existing**, not caused by the box/alias patch — proven by running the same gate on the pre-fix backup (also `missing: ensure`).

### v1.0.1-cyber Verification
- `zsh -n` ✅; `anf selftest` → **PASS 16 / FAIL 0**.
- Boxes: `anf_help` + `anf_banner` → **2 boxes, 0 misaligned** (widths 45 & 57 columns, counted per character).
- `anf hermes --version` → `Hermes Agent v0.21.0 (2026.8.31)` (previously failed outright).
- `anf doctor` → `amass`/`gobuster`/`suricata` now honestly **MISSING** (previously always a fake ◎).
- Additional gates in `scripts/verify.sh`: **#7** aligned boxes, **#8** alias-resistant binary lookup, **#9** `anf_hermes_locate` functional (skipped if hermes is not installed).
- `make verify` → **PASS=10 / FAIL=0** (gate #4 mapping green after `anf_ensure` was ported).

### Conservative
- Pre-fix backup: `research/archive/anf_cyber_shell_v1.0.0-cyber_pre-fix-2026-09-10.zsh`
  (sha256 `bccd633bb6272370fd53df53ca334edb75118a24d4e654235adff51f4e6cc731`, 130033 B).
- No function was removed: only 7 box blocks + 8 tool-check sites were changed, plus 3 new helpers.

## v1.0.0-cyber (Rebuilt standalone — 2026-09-09)

**Theme: ANF Cyber Shell is reborn as a standalone project focused on purple-team / cyber-ops, derived from the ANF v1.6.0 archive that contained the complete cyber module.**

### Origin & decisions
- Before 2026-09-07: a single **ANF → ANF Research Shell** script contained cyber ops + research (C2, recon, OSINT, exploit, defense + math/ML).
- 2026-09-07: the cyber module was **split** from ANF Research Shell (to keep the research repo clean), archived to
  `~/Documents/Archive/ANF-cyber-modules-v1.6/anf_research_shell_v1.6.0_FULL_local.zsh` (complete pre-split snapshot).
- 2026-09-09: the standalone project **ANF-Cyber-Shell** was rebuilt (**verbatim reuse**) from that archive, with a cyber-first identity.

### Changes over the archive (basis = v1.6.0 FULL archive, 123 functions / 2285 lines)
- **Promo rebrand**: header, banner, & help title → "ANF CYBER SHELL" (purple-team focus).
- **New version**: `ANF_VERSION=1.0.0-cyber` (in sync with CHANGELOG/README/manifest).
- **New cache dir**: `~/.cache/anf-cyber` (does not collide with the research `~/.cache/anf`).
- **Help reorganized cyber-first**: C2, Recon, OSINT, Exploitation, Defense/Blue, Post-Exploitation, C2-Detection Analytics → front; Utilities/Integrations/Output below. All functions remain (none removed).
- **Launcher `bin/anf-cyber`** (layered location resolution `ANF_LIB` → install → project) + idempotent `install.sh` + `anfrc.dist` + Makefile + `scripts/verify.sh` baseline gate.
- Supporting: LICENSE (MIT), NOTICE.md (security disclaimer), cyber-first README, .gitignore (local/sensitive operation output is not tracked).

### v1.0.0-cyber Verification (run via `scripts/verify.sh` & `make verify`)
- `zsh -n` → SYNTAX OK (2286 final lines).
- `ANF_VERSION` → `1.0.0-cyber`; cache dir → `anf-cyber`.
- `/cmd` → function mapping: **every `_ANF_CMD_DESC` entry has an `anf_<cmd>` function** (intact, 0 missing).
- Smoke (non-destructive): `encode test` → `dGVzdA==`, `hash abc` → `90015098...`.
- Launcher: `anf-cyber hash abc` → correct MD5; `anf-cyber help` → **C2 Framework** category shown.

### Conservative
- The original archive at `~/Documents/Archive/ANF-cyber-modules-v1.6/` is **unchanged** (still the single source of truth).
- ANF Research Shell (v1.6.1, `~/Documents/Projects/ANF-Research-Shell/` & `security-lab/`) is **untouched**.
- Dangerous commands (autopwn, real suricata, priv-esc, persist) are not executed by the verification gate.

---

## Pre-split history (brief; source `ANF-Research-Shell/docs/CHANGELOG.md`)
- **v15.0.0-RESEARCH** — merge v12 ∪ v14 (61 v12 operational functions + v14 hardening).
- **v1.0.0 – v1.6.1 ANF** — rebrand, enterprise/research study, then cyber split (2026-09-07).
