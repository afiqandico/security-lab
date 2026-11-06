# Security Lab

Kumpulan alat CLI untuk latihan **purple-team** dan higiene keamanan: secret
scanning, lint Dockerfile, anonimisasi log, orkestrasi scan CI/CD, framework
pengujian penetrasi, dan workbench operasi siber. Semua berjalan **lokal** —
tidak ada layanan berbayar, tidak ada telemetri, sebagian komponen hanya butuh
Python stdlib atau Go stdlib.

> ## ⚠️ Etika dan batas penggunaan
> Tool ofensif di repo ini (`pilgrims`, `ANF-Cyber-Shell`) **hanya** boleh
> dijalankan pada sistem yang Anda miliki atau yang Anda punya **izin tertulis**
> untuk diuji. Menjalankan pemindaian/eksploitasi tanpa izin adalah pelanggaran
> hukum (di Indonesia: UU ITE) dan etika profesional. Tidak ada laporan temuan
> organisasi nyata yang dipublikasikan di repo ini; semua target di dokumentasi
> adalah placeholder (`example.com`, `192.168.x.x`, `10.0.0.x`).

---

## Apa isi repo ini

| Komponen | Bahasa | Fungsi | Prasyarat | Uji |
|---|---|---|---|---|
| [`seclab`](seclab) | bash | Dispatcher: satu perintah untuk semua tool (`seclab list`, `seclab doctor`) | bash | `./seclab doctor` |
| [`sift/`](sift/) | Go | Secret scanner: 5 rule bawaan + `.siftrules` kustom, Shannon entropy, output pretty/JSON, mode `--git-diff` | Go 1.21+ | 7 unit test (`go test ./...`) |
| [`dockguard/`](dockguard/) | Python | Linter Dockerfile: 10 rule (DG001–DG010), 3 format output, exit code CI-friendly | Python 3.8+ (stdlib saja) | 47 unit test |
| [`log-anonymizer/`](log-anonymizer/) | Python | Anonimisasi log: hash, tokenisasi, k-anonymity, Laplace DP; CLI + endpoint FastAPI | Python 3.11+, `pip install -r requirements.txt` | unit test (butuh deps) |
| [`sec-scan-cli/`](sec-scan-cli/) | Python | Orkestrasi scan: `npm audit`, `pip-audit`, gitleaks (pin + SHA-256), Suricata → laporan agregat | Python 3, tool eksternal opsional | smoke test |
| [`pilgrims/`](pilgrims/) | bash | Framework pengujian: 20 modul (recon, web, AD, cloud, wireless, red-team, forensik, …) | bash, `nmap`/`curl`/`jq` (modul tertentu) | `tests/unit/run_all.sh` |
| [`anf-research-shell/`](anf-research-shell/) | zsh | Workbench riset reproduktif: statistik, ML/DL, plot, scaffold eksperimen (seed/env/run.sh), checksum | zsh ≥ 5.0, Python 3 | `make verify` (25 test) |
| [`ANF-Cyber-Shell/`](ANF-Cyber-Shell/) | zsh | Workbench cyber-ops: recon/OSINT, C2 ter-hardening, analitik deteksi C2, defense (Suricata/YARA) | zsh ≥ 5.0, tool opsional (`nmap`, `suricata`, `yara`) | `make verify` (10 gate) |
| [`runbook.md`](runbook.md) | — | Walkthrough end-to-end `sift` + `dockguard` dengan output nyata | — | — |

Setiap komponen tetap berdiri sendiri (README, lisensi, dependensi sendiri);
repo ini menyatukan distribusi dan satu entry point.

---

## Cara menjalankan

```bash
git clone https://github.com/afiqandico/security-lab.git
cd security-lab

./seclab list        # daftar tool
./seclab doctor      # cek runtime yang tersedia di mesin Anda
```

### Contoh per tool

```bash
# 1) Secret scanning (sift, Go)
cd sift && go build -ldflags="-s -w" -o sift . && cd ..
./seclab scan-secret .              # atau: ./sift/sift --json . > report.json

# 2) Lint Dockerfile (dockguard, Python stdlib)
./seclab lint-docker --format json Dockerfile

# 3) Anonimisasi log (log-anonymizer; butuh deps)
cd log-anonymizer && pip install -r requirements.txt && cd ..
./seclab anon-log data/sample.csv /tmp/out.csv --algorithm hash --columns ip

# 4) Pipeline scan CI (sec-scan-cli)
./seclab scan --help
./seclab ci-scan gitleaks           # jalankan satu scanner

# 5) Workbench (zsh)
./seclab anf encode hello           # → aGVsbG8=
./seclab anf-cyber selftest
```

Prasyarat minimum: `git`, `bash`, `python3`. Untuk komponen tertentu: `go`
(sift), `zsh` (ANF shells), dan tool eksternal yang memang dipanggil (mis.
`nmap`, `suricata`, `yara`, `gitleaks`) — semua opsional dan dideteksi oleh
`seclab doctor`/`anf doctor`.

---

## Status verifikasi (2026-11-06)

Dijalankan di Linux (kernel 7.0), Go 1.27.1, Python 3.14.9, zsh 5.9:

| Cek | Hasil |
|---|---|
| `cd sift && go build -ldflags="-s -w" -o sift .` | OK — binary ±3 MB |
| `cd sift && go test ./...` | OK (7 test) |
| `cd dockguard && python3 -m pytest tests` | 47 test, semua lulus |
| `bash ANF-Cyber-Shell/scripts/verify.sh` | PASS 10 / FAIL 0 |
| `cd anf-research-shell && make verify` | `zsh -n` OK, 25/25 test lulus |
| `./seclab doctor` | semua runtime terdeteksi |
| `gitleaks detect --no-git --redact` | **0 leak** (lihat [`.gitleaks.toml`](.gitleaks.toml)) |

Catatan jujur:

- `log-anonymizer` butuh dependensi pihak ketiga (`pandas`, `click`, `numpy`,
  `scikit-learn`, `cryptography`, `fastapi`); test-nya tidak dijalankan tanpa
  `pip install -r requirements.txt`.
- `sec-scan-cli` memanggil tool eksternal; bila tool tidak ada, scanner terkait
  dilewati dan dilaporkan sebagai *skipped*, bukan gagal.
- `pilgrims` dan `ANF-Cyber-Shell` adalah **wrapper otomasi**: hasilnya
  bergantung pada tool yang terpasang dan pada target yang diuji.

---

## Struktur

```
security-lab/
├── seclab                  # dispatcher CLI
├── runbook.md              # walkthrough sift + dockguard
├── .gitleaks.toml          # allowlist sempit untuk contoh dokumentasi
├── sift/                   # Go  — secret scanner
├── dockguard/              # Py  — Dockerfile linter
├── log-anonymizer/         # Py  — anonimisasi log
├── sec-scan-cli/           # Py  — orkestrasi scan CI/CD
├── pilgrims/               # sh  — 20 modul pengujian
├── anf-research-shell/     # zsh — workbench riset
└── ANF-Cyber-Shell/        # zsh — workbench cyber-ops
```

---

## Etika, batasan, dan keamanan

- **Izin tertulis wajib.** Recon/scan/eksploitasi hanya untuk aset sendiri atau
  yang diizinkan secara tertulis. Dokumentasi ini tidak menganjurkan pengujian
  tanpa izin.
- **Lab-only.** Modul C2/eksploit/persistensi di `ANF-Cyber-Shell` dan
  `pilgrims` ditujukan untuk lab terisolasi, bukan produksi.
- **Bukan pengganti penilaian ahli.** Tool ini mengotomasi pemeriksaan yang
  repetitif; keputusan keamanan tetap butuh manusia.
- **Tanpa jaminan.** MIT, tanpa garansi apa pun (lihat [`LICENSE`](LICENSE)).
- **Placeholder, bukan kredensial.** Nilai rahasia di dokumentasi/test adalah
  contoh palsu. Jangan pernah commit kredensial nyata; `.gitleaks.toml`
  sengaja dibuat sempit agar rahasia baru tetap terdeteksi.
- **Melaporkan masalah keamanan:** lihat [`SECURITY.md`](SECURITY.md) (gunakan
  GitHub Security Advisories, bukan issue publik).

## Kontribusi

Lihat [`CONTRIBUTING.md`](CONTRIBUTING.md). Untuk perubahan besar, buka issue
lebih dahulu agar arah perubahan disepakati.

## Lisensi

MIT — lihat [`LICENSE`](LICENSE). Komponen yang membawa lisensinya sendiri
tetap mengikuti lisensi di subfoldernya.
