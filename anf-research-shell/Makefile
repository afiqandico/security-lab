# ANF Research Shell — Makefile
# `make setup` : buat .venv + install requirements (opsional; hanya untuk command ML/DL).
# `make verify`: gate ringan & deterministik — syntax zsh, 25 test Python, smoke launcher.
# `make test`  : alias verify.
.PHONY: setup verify test

setup:
	python3 -m venv .venv
	.venv/bin/pip install --upgrade pip
	.venv/bin/pip install -r requirements.txt
	@echo "venv siap. Jalankan: make verify"

verify:
	@echo "== 1/3 zsh -n (syntax) =="
	zsh -n code/anf_research_shell.zsh
	@echo "== 2/3 test suite (25 test deterministik) =="
	python3 code/test_suite.py
	@echo "== 3/3 smoke launcher =="
	@test "$$(zsh bin/anf encode hello 2>/dev/null)" = "aGVsbG8=" && echo "  encode hello -> aGVsbG8="
	@zsh bin/anf hash abc 2>/dev/null | grep -q 900150983cd24fb0d6963f7d28e17f72 && echo "  hash abc -> MD5 90015098..."
	@echo "OK"

test: verify
