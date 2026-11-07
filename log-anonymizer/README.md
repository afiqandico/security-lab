# Log Anonymizer

**Privacy-preserving toolkit for log data anonymization**. Provides:

- **CLI** (Click) with the sub-commands `hash`, `tokenize`, `k_anonymity`, `diff_privacy`.
- **FastAPI** endpoint `/anonymize` (POST) for anonymization over HTTP.
- **Docker** image that can run as a standalone service.
- **Unit tests** (pytest) for each method.

## Key features
| Method | Description |
|--------|-------------|
| `hash` | SHA-256 hashing (optional salt). |
| `tokenize` | Deterministic UUID token replacement. |
| `k_anonymity` | Quasi-identifier generalization with a `k` parameter. |
| `diff_privacy` | Adds Laplace noise to numeric columns (`epsilon` parameter). |

## Installation
```bash
# Clone repo
git clone https://github.com/afiqandico/security-lab.git
cd log-anonymizer
# Virtual env (optional)
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
```

## CLI usage
```bash
# Hash an entire text file
python -m log_anonymizer.cli run input.log output_hashed.log --algorithm hash

# Tokenize a CSV (columns ip,userid)
python -m log_anonymizer.cli run data.csv data_tok.csv --algorithm tokenize --columns ip userid

# k-anonymity on a CSV (quasi-identifiers timestamp,userid)
python -m log_anonymizer.cli run data.csv data_kanon.csv --algorithm k_anonymity --columns timestamp userid --k 5

# Differential privacy (Laplace noise)
python -m log_anonymizer.cli run data.csv data_dp.csv --algorithm diff_privacy --columns amount price --epsilon 0.8
```

## FastAPI (Remote)
```bash
# Run the server
uvicorn src.api:app --host 0.0.0.0 --port 8000
# or via Docker
docker compose up --build -d
```

### Example request (curl)
```bash
curl -X POST http://localhost:8000/anonymize \
  -H "Content-Type: application/json" \
  -d '{"log":"user=alice ip=10.0.0.5 action=login","method":"hash"}'
```

## Docker
```bash
# Build and run all services
docker compose up --build -d
# API available at http://localhost:8000, proxy at http://localhost:8080
```

## Testing
```bash
pytest -n auto   # run all tests in parallel in src/tests/
```

## Roadmap
- **v0.2.0** – log streaming support (Kafka/Fluentd).
- **v0.3.0** – policy engine (YAML) for per-field configuration.
- **v0.4.0** – Prometheus metrics + Grafana dashboard.
- **v1.0.0** – complete documentation, CI/CD pipeline examples.

## License
MIT – see the `LICENSE` file.
