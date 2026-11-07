# Runbook — Security Lab Workflow

An example end-to-end flow using the two core tools (`sift` + `dockguard`). All output
below comes from real executions (re-verified 2026-11-06; sift v1.0.0,
dockguard v1.0.0, Go 1.27, Python 3.14). The secret values in the examples are
obviously fake placeholders — never put real credentials in a repo.

## 0. Setup

```bash
# build sift (Go stdlib, no dependencies)
cd sift
go build -ldflags="-s -w" -o sift .
./sift --version        # → Sift v1.0.0

# dockguard only needs the Python stdlib
cd ../dockguard
python3 -m dockguard --version
```

## 1. Example of problematic code

```python
# app.py
def handler():
    api_key = "sk-proj-EXAMPLEEXAMPLE1234567890"   # hardcoded secret!
    print("ok")
```

```dockerfile
# Dockerfile
FROM node:20-alpine
CMD ["node", "server.js"]       # no USER / HEALTHCHECK
```

Save it as `/tmp/demo/Dockerfile`.

## 2. Scan for secrets with sift

```bash
./sift/sift /tmp/demo
```

Actual output:

```
🔍 Sift v1.0.0: Smart & Extensible Security Scanner

✅ Loaded 5 default rules.

⚠️  Found 1 issues in 163.325µs:

[HIGH] Generic Secret/Token (S002)
  /tmp/demo/app.py:2
  | api_key = "sk-proj-EXAMPLEEXAMPLE1234567890"
```

## 3. Lint the Dockerfile with dockguard

```bash
python3 -m dockguard --format json --no-color /tmp/demo/Dockerfile
```

Actual output (abridged):

```json
{
  "tool": "dockguard",
  "version": "1.0.0",
  "findings": [
    { "rule_id": "DG001", "severity": "warning",
      "message": "No USER instruction — container will run as root by default",
      "line": 2 },
    { "rule_id": "DG007", "severity": "info",
      "message": "No HEALTHCHECK instruction defined", "line": 2 }
  ],
  "summary": { "error": 0, "warning": 1, "info": 1 }
}
```

CI-friendly mode (exit code only):

```bash
python3 -m dockguard --quiet /tmp/demo/Dockerfile
echo $?    # → 1 (findings present; 0 = clean)
```

## 4. Fix and verify

```dockerfile
# Dockerfile (clean)
FROM node:20-alpine
USER node
HEALTHCHECK CMD wget -q --spider http://localhost:3000 || exit 1
CMD ["node", "server.js"]
```

```bash
python3 -m dockguard --quiet Dockerfile
echo $?    # → 0 = clean, ready to move on to the pipeline
```

## 5. Minimal CI integration

```yaml
      - name: Scan secrets
        run: ./sift .
      - name: Lint Dockerfiles
        run: |
          find . -name 'Dockerfile*' -exec python3 -m dockguard --quiet {} \;
```

## Notes

- Exit code: dockguard 0 = clean, 1 = findings present, 2 = file not found.
- Other tools in this repo: `log-anonymizer`, `sec-scan-cli`, `pilgrims` — see each README.
- Use only in *authorized* environments (see the root README — Ethics Note).