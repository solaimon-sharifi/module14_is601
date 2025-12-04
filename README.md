# Calculator BREAD App

FastAPI + SQLAlchemy + Playwright starter for the Module 14 calculator assignment. Users can register, authenticate, and perform full BREAD (Browse / Read / Edit / Add / Delete) calculations that stay scoped to their account.

## Requirements

- Python 3.10+ (tested with 3.10.13)
- PostgreSQL (local or Docker)
- [Playwright](https://playwright.dev/python/docs/intro) for the UI tests
- Docker / Docker Compose for containerized runs

## Local Setup

1. Create and activate the project virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

2. Upgrade pip and install the pinned dependencies:

```bash
pip install --upgrade pip
pip install -r requirements.txt
playwright install
```

3. Keep the virtual environment active whenever you work in this repo. You can also prefix tools with `.venv/bin/` (e.g., `.venv/bin/pytest`) if you forget to activate; this guarantees the same dependency set without relying on the system Python.

4. Copy `.env.example` (if present) or export the required environment variables such as `DATABASE_URL`, `JWT_SECRET_KEY`, etc. Defaults in `app/core/config.py` point at `postgresql://postgres:postgres@localhost:5432/fastapi_db`.

## Running the App

Ensure the database is available, initialize it, and launch the server from the repo root:

```bash
python -m app.database_init
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Visit `http://localhost:8000` to login, register, and exercise the dashboard templates. The `/calculations` API layer enforces per-user scoping, and the frontend templates cover list, detail, edit, and delete views.

## Running Tests Locally

```bash
source .venv/bin/activate
pytest tests/unit/
pytest tests/integration/
pytest tests/e2e/
```

Playwright-based E2E tests exercise login + BREAD flows through the UI. The `tests/conftest.py` fixture boots the FastAPI server and a Playwright browser session automatically.

## Docker

Build the image and run a container:

```bash
docker build -t solaimon/module14_is601:latest .
docker run --rm -p 8000:8000 solaimon/module14_is601:latest
```

Or use Docker Compose to run the web app alongside PostgreSQL and pgAdmin:

```bash
docker-compose up --build
```

The Compose file exposes the calculator at `http://localhost:8000` and pgAdmin at `http://localhost:5050` (admin@example.com / admin).

You can automate a full rebuild and log check with the supplied helper script:

```bash
chmod +x scripts/docker_rebuild_logs.sh
scripts/docker_rebuild_logs.sh
```
This script runs `docker-compose down`, rebuilds the services with `--no-cache`, brings everything up detached, and streams the latest logs so you can verify startup.

## CI/CD & Docker Hub

GitHub Actions (`.github/workflows/test.yml`) now:

1. Boots a PostgreSQL service
2. Installs dependencies + Playwright browsers
3. Runs unit, integration, and E2E suites (pytest)
4. Builds a Docker image and scans it with Trivy
5. Pushes multi-arch image to Docker Hub as `solaimon/module14_is601:latest` and `solaimon/module14_is601:${{ github.sha }}` when `DOCKERHUB_USERNAME`/`DOCKERHUB_TOKEN` secrets are provided and the `main` branch is updated

Verify the workflow run and Docker Hub tags via the GitHub Actions logs/history for grading artifacts.

## Reflection / Follow-up

- See `REFLECTION.md` for prompts you can expand on before submission.
- Update the `docs/` directory if you extend the feature set beyond the base assignment.
