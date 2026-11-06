# ANF Research Shell

[![CI](../../actions/workflows/anf-research-shell-ci.yml/badge.svg)](../../actions/workflows/anf-research-shell-ci.yml)

**Integrated & Reproducible Research Workbench — satu terminal untuk pekerjaan peneliti (science, math, CS), dengan output terukur dan tertelusur.**
*v1.0.0 = struktur enterprise/research; v1.5.0+ = fokus research workbench.*

ANF membungkus perhitungan ilmiah, ML/DL, visualisasi, scaffolding eksperimen, dan verifikasi reproducibility ke satu CLI yang deterministik dan teruji.

---

## Kenapa ANF (positioning — satu cerita)
**ANF = Integrated & Reproducible Research Workbench.** Bukan sekadar shell, tapi satu terminal tempat semua pekerjaan peneliti (science, math, computer science) bisa dijalankan **konsisten, terukur, dan tertelusur**:

- **Science & Math** — statistik, kalkulator, matematika simbolik, visualisasi
- **ML / DL / Eval** — stack-check, benchmark deterministik, DL smoke test, scaffold eksperimen, data wrangling, bibliografi
- **Reproducible** — seed deterministik, 25-test suite, scaffold eksperimen (`seed.txt`/`env.txt`/`run.sh`) per run
- **Self-contained / Rapi** — auto-install tool Linux (`/ensure`), installer, git, docs

Jalankan `anf why` untuk ringkasan singkat dan `anf help` untuk daftar command.

---

## Instalasi
```bash
./install.sh                 # instal ke /usr/local/bin + config + lib
# atau tanpa root:
./install.sh --prefix "$HOME/.local"
```

## Penggunaan
```bash
anf                # shell interaktif (ZLE popup + banner)
anf help           # daftar semua command
anf encode hello   # aGVsbG8=
anf hash abc       # MD5/SHA256/SHA512
anf status         # status sesi
anf envcard        # ringkasan sistem & tools
anf task start demo "python3 train.py"   # task tmux latar (survive restart)
anf task list      # daftar task
anf q "pattern" .  # pencarian cepat grep
anf gitx s         # git status ringkas
anf gen "buat script backup"   # AI-assisted code gen (hermes/opencode)
```

Di dalam shell: `/gen`, `/task`, `/q`, `/gitx`, `/envcard`, `/mvp` (alias `/cmd`).

## Research Suite — untuk peneliti (science, math, computer science)
Semua pekerjaan riset dalam satu terminal:
```bash
anf mlinfo                    # stack ML/DL: torch/sklearn/numpy/CUDA
anf stat data.txt             # statistik dataset: mean/median/std/entropy
anf mlbench 400               # benchmark deterministik → research/outputs/*.csv
anf dlcheck                   # smoke test DL CPU (torch, seed=42)
anf dlcheck full              # mini-MLP 3 epoch synthetic
anf paperstat manuscript.md   # statistik kata/baris/ref manuskrip
```
Fungsi historis (mis. `anf matrix` / MITRE ATT&CK, `c2-*`) tetap tersedia untuk kompatibilitas
namun tidak dipromosikan sejak v1.6.0 — lihat CHANGELOG.
Hasil benchmark ditulis otomatis ke `research/outputs/` — bukti numerik untuk reproducibility.

## Researcher Workflow (semua via terminal)
```bash
anf plot data.csv 2 hist "judul"   # visualisasi -> research/outputs/*.png
anf calc "sqrt(144)"               # kalkulator ilmiah
anf sym "integrate(x**2, x)"       # matematika simbolik (sympy)
anf sha manuscript.tex             # checksum reproducibility
anf exp nama_eksperimen            # scaffold: folder+seed+env+run.sh
anf csv summary data.csv           # data wrangling (pandas)
anf bib references.bib             # validasi bibliografi
```

## Auto-Provisioning (Linux)
Tool yang belum terpasang otomatis di-install:
```bash
anf ensure doctor   # cek tool wajib (dry, aman)
anf ensure all      # auto-install yang kurang (konfirmasi dulu, butuh sudo)
```
Mendukung apt (Debian/Ubuntu), dnf (Fedora), pacman (Arch), brew (fallback). Fokus **Linux dulu** untuk stabilitas.

## Verifikasi (reproducibility)
```bash
zsh -n code/anf_research_shell.zsh        # MUST: SYNTAX OK
python3 code/test_suite.py                # MUST: 25/25 pass
bin/anf hash abc                          # MD5 90015098...
```

## Verifikasi & reproduksibilitas
```bash
make verify    # zsh -n + 25 test deterministik + smoke test launcher
make setup     # opsional: .venv + requirements.txt (untuk command ML/DL)
```

`make verify` hanya membutuhkan **zsh + Python 3 stdlib** — tanpa dataset eksternal,
tanpa venv, tanpa jaringan. Gate-nya: syntax script inti, 25 unit test
(entropy, ROC, GuardFall, beaconing, C2 math), lalu smoke launcher (`encode`, `hash`).

Artefak eksperimen (`research/outputs/*.csv|*.png`) dihasilkan oleh `anf mlbench`,
`anf plot`, dan `anf exp`; berkas hasil tidak di-commit (lihat `.gitignore`).
Authorship & AI disclosure: lihat `NOTICE.md`.

---

## Struktur
```
anf-research-shell/
├── bin/anf                  # launcher CLI
├── Makefile                 # make setup / verify / test
├── requirements.txt         # dependensi pin opsional (command ML/DL)
├── LICENSE                  # MIT (kode)
├── NOTICE.md                # authorship + AI disclosure + pernyataan standalone
├── .github/workflows/ci.yml # CI: make verify di push/PR
├── code/anf_research_shell.zsh  # script inti (64 fungsi, fokus riset; modul cyber diarsipkan terpisah)
├── code/test_suite.py       # 25 test deterministik
├── code/anfrc.dist          # template konfigurasi
├── docs/CHANGELOG.md        # riwayat versi
└── install.sh               # installer
```

## Kontribusi & etika
Tool untuk **riset keamanan dan edukasi** (lingkungan lab/sendiri). Tidak untuk aktivitas ilegal. Penggunaan di luar lingkungan Anda sendiri menjadi tanggung jawab pengguna.

## AI disclosure
Tool dikembangkan & didokumentasikan dengan bantuan AI (Hermes, model DeepSeek V4 Flash). Penulis utama: **Afiq Andico Pangimpian** (Independent Researcher).

## Lisensi
Kode: **MIT** (lihat `LICENSE`, 2026).
