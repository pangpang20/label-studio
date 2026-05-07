# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Label Studio is an open source data labeling tool supporting audio, text, images, videos, and time series. Backend is Django 5.1 + DRF; frontend is React 18 + TypeScript in an Nx monorepo.

## Common Commands

### Backend (Python/Django)

```bash
make run-dev                          # Dev server with SQLite at http://localhost:8080
make migrate-dev                      # Run DB migrations (SQLite)
python label_studio/manage.py migrate # Manual migration run

# Run all tests
make test                             # SQLite, excludes integration_tests

# Run a single test
cd label_studio && DJANGO_DB=sqlite pytest tests/test_core.py::test_name -v

# Lint/format
make fmt                              # Format changed files (pre-commit)
make fmt-all                          # Format all files
make fmt-check                        # Lint check changed files
```

### Frontend (React/TypeScript)

```bash
make frontend-install                 # cd web && yarn install --frozen-lockfile
make frontend-dev                     # HMR dev server at localhost:3000
make frontend-build                   # Production bundle to web/dist/

cd web && yarn test:unit              # Jest unit tests
cd web && yarn ls:e2e                 # Cypress e2e tests
cd web && yarn lsf:integration        # Cypress integration tests
```

### Docker

```bash
make docker-run-dev                   # docker compose up --build
docker-compose up                     # Label Studio + Nginx + PostgreSQL
docker compose -f docker-compose.yml -f docker-compose.minio.yml up -d  # + MinIO S3
```

## Architecture

### Backend (`label_studio/`)

- **Entry point**: `label_studio/server.py` (console script `label-studio`)
- **Manage script**: `label_studio/manage.py`
- **Settings**: `label_studio/core/settings/label_studio.py` → imports from `base.py`
- **URL routing**: `label_studio/core/urls.py` (base), per-app `urls.py` in each app
- **Django apps**: `core`, `projects`, `tasks`, `users`, `organizations`, `data_import`, `data_export`, `data_manager`, `io_storages`, `ml`, `ml_models`, `ml_model_providers`, `webhooks`, `jwt_auth`, `labels_manager`, `session_policy`, `fsm`

### Frontend (`web/`)

- **Nx monorepo** with webpack, TypeScript 5.8, Tailwind CSS 3.4
- **Main app**: `web/apps/labelstudio/`
- **Shared libs**: `web/libs/editor/` (annotation UI), `web/libs/datamanager/`, `web/libs/ui/` (design system)
- **State management**: Jotai atoms (not Context API for shared state)
- **Linting**: Biome (replaces ESLint/Prettier), config in `web/biome.json`
- **Storybook**: `yarn nx storybook storybook` (port 4400)

### Database

- **Dev**: SQLite (`DJANGO_DB=sqlite`)
- **Prod**: PostgreSQL (psycopg3), MySQL also supported via `docker-compose.mysql.yml`
- **Key env vars**: `DJANGO_DB`, `POSTGRE_HOST`, `POSTGRE_PORT`, `POSTGRE_USER`, `POSTGRE_PASSWORD`, `POSTGRE_NAME`

## Python Conventions

- **Ruff** for lint + format: line-length 119, single quotes (config in `pyproject.toml`)
- **pre-commit** hooks in `.pre-commit-config.yaml` and `.pre-commit-dev.yaml`
- **Async migrations** for long-running DDL: use `atomic = False`, `CREATE INDEX CONCURRENTLY`, and `start_job_async_or_sync`. See `.cursor/rules/async_migrations.mdc` for template.

## Frontend Conventions

- Functional components only, keep components small and focused
- Use Jotai atoms for shared state; `atomWithQuery` for API requests
- Prefer `useMemo`/`useCallback` for memoization; avoid unnecessary `useEffect`
- **Design system**: Use `@humansignal/ui` components (Button, Modal, Message, etc.) — never raw `<button>`
- **Tokens**: Use semantic tokens (`p-tight`, `bg-primary-surface`, `text-body-medium`) — never numeric tokens (`p-200`, `bg-grape-600`)
- **CSS**: Tailwind utilities preferred; CSS Modules with semantic tokens for component-specific styles
- **Imports**: `import { cn } from '@humansignal/core'`; `import { getTokenColor } from '@humansignal/ui'` for canvas colors
- **Button hierarchy**: Only ONE primary/filled button per screen
- **Naming**: `@humansignal/ui` components use kebab-case files; app components use PascalCase

## Testing

- **Backend**: pytest + pytest-django + Tavern (API tests as `.tavern.yml`), config in `label_studio/pytest.ini`
- **Frontend unit**: Jest via `yarn test:unit`
- **Frontend e2e**: Cypress tests in `web/libs/editor/tests/integration/e2e/`
- Cypress helpers imported from `@humansignal/frontend-test/helpers/LSF`

## Key Environment Variables

| Variable | Purpose |
|----------|---------|
| `DJANGO_DB` | Database backend: `sqlite` or `default` (postgres) |
| `DEBUG` | Enable debug mode |
| `LOG_LEVEL` | Logging level |
| `LABEL_STUDIO_HOST` | Hostname for the application |
| `SECRET_KEY` | Django secret key |
| `POSTGRE_*` | PostgreSQL connection settings |
