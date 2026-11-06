# plathea-soft
Software in development to automate tasks on commerce


# Platea Restaurant API

Backend version **0.1.0**, built with **FastAPI and Python** from the restaurant platform reference images. React/TypeScript, PostgreSQL, and external service connections are left for you to implement.

The API covers overview, POS/orders, tables and reservations, digital menu, inventory, team/permissions, branches, analytics, and AI-agent configuration and conversation records.

This is a **local development prototype**. Records live in memory and reset on restart or reload. Development identity headers let you exercise permission and tenant rules; they do not authenticate users. Production startup is disabled until real authentication and durable persistence are implemented.

## Run locally

Requires Python **3.12–3.14**. Tested on **Python 3.13.2 / Windows**. No database, Node.js, or provider credentials are required.

From this directory in PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements-dev.txt
Copy-Item .env.example .env
.\.venv\Scripts\python.exe -m uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

On macOS/Linux, use `python3 -m venv .venv` and `.venv/bin/python` for the remaining commands. The tested dependency constraints were captured on Windows; platform-only Uvicorn extras may resolve separately on Linux.

Open:

- Swagger UI: http://127.0.0.1:8000/docs
- ReDoc: http://127.0.0.1:8000/redoc
- OpenAPI: http://127.0.0.1:8000/openapi.json
- Health: http://127.0.0.1:8000/health/live

Use **one worker**. Multiple workers have independent memory and must not be used for shared state.

## Try an endpoint

The demo contains three branches, six products, two employees/roles, tables, two ingredients, a supplier, and a disabled agent. Sales and stock start empty; no customer or historical sales data is fabricated.

```powershell
$headers = @{
    'X-Tenant-ID' = '08193635-07b7-525d-b523-226b9c12fc36'
    'X-Actor-ID' = 'bbcc4170-7bc8-560a-b2d2-b86bf4d6c6f6'
}
Invoke-RestMethod 'http://127.0.0.1:8000/api/v1/overview' -Headers $headers
Invoke-RestMethod 'http://127.0.0.1:8000/api/v1/menu/products' -Headers $headers
```

These IDs also work in Swagger's header parameters. Use the waiter actor `c3e2eeb6-3eb2-558a-9bc2-981211637ae3` to exercise branch restrictions.

## Documentation

- [Backend guide](docs/BACKEND_GUIDE.md): requirements, versions, image mapping, development process, architecture, business rules, examples, PostgreSQL plan, and remaining work.
- [Every API endpoint](docs/API_ENDPOINTS.md): generated method/path inventory.
- [OpenAPI JSON](docs/openapi.json): machine-readable contract for your future TypeScript client.
- [Change log](CHANGELOG.md): release history.

## Verify or update the contract

```powershell
.\.venv\Scripts\python.exe -m pytest -q -p no:cacheprovider
.\.venv\Scripts\python.exe -m ruff check app tests scripts
.\.venv\Scripts\python.exe -m ruff format --check app tests scripts
.\.venv\Scripts\python.exe scripts/export_contract.py
```

Install runtime dependencies only with `pip install -r requirements.txt`. The `.venv` created during development is ignored; recreate it using the requirements files on another machine.
