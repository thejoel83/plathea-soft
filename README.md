# Platea — restaurant SaaS

**Version 0.3.0** · React + TypeScript · FastAPI · PostgreSQL · Linux/Docker deployment.

The original backend and restaurant workflows are retained. This release integrates Better Auth into the existing React/FastAPI platform, with a private Node authentication service, PostgreSQL auth schema, native account/session lifecycle, restaurant membership bindings and updated deployment/verification procedures.

**No software or dependencies were installed on the Windows PC during this work.** The production server does not exist yet. This is a prepared source/deployment release candidate; Linux database, frontend build, browser and live-server gates must pass before it is treated as production verified.

## Start here

- [Documentation index](docs/DOCUMENTATION_INDEX.md): current procedures, process history and verification status.
- [Better Auth integration](docs/AUTHENTICATION.md): architecture, endpoint contract, migration, provisioning and recovery.
- [Linux deployment procedure](docs/DEPLOYMENT.md): server prerequisites, domain, secrets, isolated checks, launch, owner account, backups and recovery.
- [Application and architecture](docs/RELEASE_GUIDE.md): delivered screens/features, authentication, persistence, versions and known boundaries.
- [Test evidence and pending gates](docs/TESTING.md), plus [machine-readable results](docs/validation-results.json).
- [Every API endpoint](docs/API_ENDPOINTS.md) and [OpenAPI contract](docs/openapi.json).
- [Original backend development record](docs/BACKEND_GUIDE.md), preserved as historical 0.1.0 documentation.

## Deploy later on Linux

Transfer the source archive/repository to the future server, then follow the full deployment guide. The short sequence is:

```bash
# Run ONLY on Linux with Docker Engine/BuildKit and Compose 2.24+ already available.
bash deploy/prepare.sh app.your-restaurant-domain.com admin@your-restaurant-domain.com
bash deploy/release.sh .env.production
docker compose --env-file .env.production run --rm api python -m app.manage create-owner
```

The preparation script creates private file secrets without printing their values. The release script validates settings and builds/tests in isolated containers before starting production services. It downloads dependencies inside server/CI images, not onto the development PC. No default production password or demo account is shipped. The owner command prompts for credentials without echoing the password.

Open `https://YOUR_DOMAIN` after DNS, HTTPS and provisioning succeed. FastAPI, Better Auth and PostgreSQL have no published public ports; Caddy serves the frontend and proxies same-origin API requests. Data survives container restarts in a PostgreSQL volume.

## Verify without deploying production

On Linux/CI with Docker:

```bash
bash deploy/verify.sh
```

This builds the frontend with strict TypeScript checks, runs backend and frontend logic tests, exercises real PostgreSQL persistence/concurrency/constraints, tests database dump/restore and runs browser flows. It uses a disposable `platea-check-*` stack, not production data.

Existing local Python tools can still run the memory-backed unit suite without installing anything:

```powershell
.\.venv\Scripts\python.exe -m pytest -q -p no:cacheprovider
.\.venv\Scripts\python.exe -m ruff check app tests scripts
```

The five PostgreSQL tests skip when no dedicated test database is configured. Skipped tests are not evidence of database verification. Local `.venv` metadata belongs to the original development environment and was not reinstalled to match the new production extras.

## Included sections

Overview, POS/orders, payments/pre-bills, tables/reservations, digital menu/public QR menu, inventory/purchases/suppliers, team/permissions/shifts, branches, analytics/expenses, agent configuration/local conversations, settings and help.

Payments are manual records of payments received elsewhere. WhatsApp, autonomous AI replies, SMTP, gateway charging/refunds, SaaS subscriptions and fiscal invoicing need separate provider integration. Inventory movements are explicit; automatic recipe consumption is not implemented. The release guide explains these limits.

## Source and artifacts

- `app/`: backend, verified identity/restaurant membership bridge and business migrations.
- `auth-service/`: Better Auth 1.7.20 on Node 24, auth migrations and native integration tests.
- `frontend/`: React/TypeScript application and browser/logic tests.
- `deploy/` and `compose.yaml`: Linux containers, HTTPS, verification, backups and release scripts.
- `scripts/export_contract.py` / `export_types.py`: regenerate the API contract and frontend model types using existing Python tools.
- `scripts/package_release.py`: produce a sanitized `.tar.gz` plus checksum/source manifest under `release/`.

Python dependency constraints are retained; production extras and frontend top-level versions are declared. Missing frontend/auth-service locks are generated automatically inside Docker on the build server; preserve/review it. Immutable image digests and security scans still require the Linux release process. See the deployment guide before claiming a fully reproducible production build.
