# Contributing

## Development Setup

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and Docker Compose
- [Python 3.13+](https://www.python.org/) (for local backend development)
- [Node.js 20+](https://nodejs.org/) and [pnpm](https://pnpm.io/) (for local frontend development)
- [just](https://github.com/casey/just) command runner (optional but recommended)

### Getting Started

```bash
# Clone the repo
git clone https://github.com/adityasinh-jadeja/api-scanner.git
cd api-scanner

# Copy environment variables
cp .env.example .env

# Start development environment
docker compose -f dev.compose.yml up --build
```

The app will be available at:
- **Dashboard:** http://localhost
- **API Docs:** http://localhost:8000/docs
- **Frontend (direct):** http://localhost:5173

## Code Style

### Backend (Python)

- **Formatter/Linter:** [Ruff](https://github.com/astral-sh/ruff) — configured in `pyproject.toml`
- **Type Checking:** [MyPy](https://mypy-lang.org/) — strict mode with per-file overrides
- **Code Quality:** [Pylint](https://pylint.org/) with Pydantic plugin
- **Security:** [Bandit](https://bandit.readthedocs.io/) for security-focused static analysis

```bash
# Run all backend linters
cd backend
ruff check .
mypy .
pylint .
bandit -r . -c pyproject.toml
```

### Frontend (TypeScript)

- **Formatter/Linter:** [Biome](https://biomejs.dev/) — configured in `biome.json`
- **Additional Linting:** ESLint with React and accessibility plugins
- **Formatting:** Prettier as fallback
- **Style Linting:** Stylelint for CSS

```bash
# Run all frontend linters
cd frontend
pnpm lint
pnpm typecheck
pnpm lint:scss
```

## Architecture

The project follows a layered architecture:

```
Routes → Services → Repositories → Models
                  → Scanners → External Target API
```

- **Routes** handle HTTP request/response and validation
- **Services** contain business logic and orchestrate scanners
- **Repositories** encapsulate all database queries
- **Scanners** inherit from `BaseScanner` and implement the `scan()` method
- **Schemas** (Pydantic) validate all input/output data

## Commit Conventions

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: add XSS scanner module
fix: handle timeout in rate limit scanner
docs: update API endpoint documentation
refactor: extract common scanner logic
test: add unit tests for auth service
ci: add Docker build step to pipeline
```

## Pre-commit Hooks

The project uses [pre-commit](https://pre-commit.com/) for automated checks:

```bash
pip install pre-commit
pre-commit install
```

This runs Ruff, MyPy, Pylint, and YAML/TOML validation on every commit.

## Adding a New Scanner

1. Create a new file in `backend/scanners/` (e.g., `xss_scanner.py`)
2. Inherit from `BaseScanner` and implement `scan() -> TestResultCreate`
3. Add a new `TestType` enum value in `backend/core/enums.py`
4. Register the scanner in `backend/services/scan_service.py` in the `scanner_mapping` dict
5. Add payloads in `backend/scanners/payloads.py`
6. Update the frontend `SCAN_TEST_TYPES` in `frontend/src/config/constants.ts`
