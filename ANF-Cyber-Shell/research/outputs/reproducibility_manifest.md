# Reproducibility Manifest — ANF Cyber Shell

**Version:** 1.0.4-cyber
**Verification date:** 2026-11-06 (v1.0.4-cyber, re-run on Linux + zsh 5.9)
**Command:** `make verify` (or `bash scripts/verify.sh`)

## Baseline gates (all PASS, non-destructive)
| # | Gate | Method | Status |
|---|------|--------|--------|
| 1 | zsh syntax parse | `zsh -n code/anf_cyber_shell.zsh` | ✅ |
| 2 | Consistent version | `ANF_VERSION == 1.0.0-cyber` | ✅ |
| 3 | Separate cache dir | `~/.cache/anf-cyber` (does not collide with research) | ✅ |
| 4 | `/cmd`→function mapping | every `_ANF_CMD_DESC` entry has `anf_<cmd>` | ✅ (intact) |
| 5 | Core command smoke | `encode test`→`dGVzdA==`, `hash abc`→MD5 `90015098...` | ✅ |
| 6 | Launcher hash | `anf-cyber hash abc` | ✅ |
| 7 | Launcher help | `C2 Framework` category shown | ✅ |
| 8 | Banner/help boxes aligned | width per character (python3), `anf_help` + `anf_banner` | ✅ (2 boxes, 0 misaligned) |
| 9 | Alias-resistant binary lookup | no `command -v <command-name>` in the core script | ✅ |
| 10 | `anf_hermes` functional | `anf_hermes_locate` → real executable path (skipped if hermes is absent) | ✅ |

## Bugfix v1.0.1-cyber (2026-09-10)
| Symptom | Root cause | Fix | Evidence |
|--------|--------------|-----------|-------|
| Banner border & `help` title skewed (right banner border missing) | hardcoded `═══` border, title contains `${ANF_VERSION}` | Helper `_anf_box` (width computed from content), used in 7 places | 2 boxes, 0 misaligned |
| `hermes` from inside ANF → `command not found: alias hermes=anf_hermes` | ANF commands registered as plain-name aliases → `command -v hermes` returns the ALIAS TEXT, not a path | `_anf_which` (`whence -p`) / `_anf_has_bin`; used in `anf_hermes_locate`, `anf_gen`, `anf_doctor`, tool wrapper (`amass`, `gobuster`, `nikto`, `whatweb`, `suricata`, `whois`) | `anf hermes --version` → `Hermes Agent v0.21.0` |
| Tool check in `anf_doctor` always ◎ (false-positive) | same as above (alias masks the binary lookup result) | doctor loop uses `_anf_has_bin` | `amass`/`gobuster`/`suricata` → honest `MISSING` |
| `ensure` shown in help but `anf ensure` → command tidak dikenal | research→cyber merge brought the `_ANF_CMD_DESC` entry without the `anf_ensure` definition (gate #4 red since the merge; the v1.0.0 manifest claim of "intact mapping" was wrong) | `anf_ensure` ported verbatim from ANF Research Shell (+ `_anf_has_bin`) | `make verify` → PASS=10 FAIL=0 |

Pre-fix backup: `research/archive/anf_cyber_shell_v1.0.0-cyber_pre-fix-2026-09-10.zsh`
(sha256 `bccd633bb6272370fd53df53ca334edb75118a24d4e654235adff51f4e6cc731`, 130033 B).

## Selftest
- `anf-cyber selftest` → **PASS 16 / FAIL 0**.

## Portable install (E2E)
- `./install.sh --prefix /tmp/...` → launcher runs from an isolated prefix
  (`…/usr/lib/anf-cyber/anf_cyber_shell.zsh`), hash + version correct. Self-contained.

## Source of truth (origin)
- The basis is derived **verbatim** from the author's archive:
  `~/Documents/Archive/ANF-cyber-modules-v1.6/anf_research_shell_v1.6.0_FULL_local.zsh`
  (pre-split ANF v1.6.0 snapshot, complete cyber module).
- Modifications over the basis (promo rebrand only, no functions removed):
  header/banner/help → cyber-first, `ANF_VERSION=1.0.0-cyber`, cache dir → `anf-cyber`.

## Recorded PITFALL (verify.sh)
- **Do not grep directly in a pipe with `set -o pipefail`**: `cmd | grep -q` → grep
  exits early → SIGPIPE → writer exits 141 → pipefail = false FAIL. Fix: capture output
  into a variable first, then `[[ var == *pat* ]]`.
- **Shell aliases mask the `command -v` result**: every ANF command is registered as a
  **plain-name** alias (`hermes=anf_hermes`), so `command -v hermes` inside an
  ANF session returns the ALIAS TEXT, not the binary path — the tool check therefore always passed
  and that alias string was itself executed. Fix: `whence -p` via the `_anf_which` /
  `_anf_has_bin` helper. Gate #9 guards against this regression.

## Disclaimer
Dangerous commands (autopwn, real suricata, priv-esc, persist, password-spray)
are **not executed** by the gate — for lab/authorized environments only.
