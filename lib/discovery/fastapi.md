# FastAPI Route Discovery

Extract all API endpoints from a FastAPI project.

## Step 1: Find the Main App

- Grep for `FastAPI()` in `**/*.py` to locate the app instance
- Common locations: `main.py`, `app.py`, `app/main.py`, `src/*/main.py`
- Note the variable name (usually `app`)

## Step 2: Find Direct Routes

Grep for route decorators on the main app:
```
@app.get|post|put|delete|patch("...")
```

Extract: HTTP method, path, function name.

## Step 3: Find Router Includes

Grep for `include_router` calls:
```python
app.include_router(some_router, prefix="/api/v1")
```

For each included router:
1. Trace the import to find the router's source file
2. Grep that file for `@router.get|post|put|delete|patch("...")`
3. Prepend the prefix from `include_router`

## Step 4: Handle Nested Routers

Routers can include other routers:
```python
router.include_router(sub_router, prefix="/users")
```

Recursively trace all router includes, accumulating prefixes.

## Step 5: Identify Route Parameters

- Path params: `{id}`, `{user_id}` → need example values for testing
- Query params: check function signature for non-path parameters
- Request body: check for Pydantic model type hints

## Route Priority Classification

- **P0:** Auth endpoints (`/login`, `/token`, `/register`), health check
- **P1:** Core CRUD endpoints for primary resources
- **P2:** Admin, config, statistics endpoints

## Output Format

```
FastAPI Routes:
  [GET]    /api/health → health_check — P0
  [POST]   /api/auth/login → login — P0
  [GET]    /api/users → list_users — P1
  [GET]    /api/users/{id} → get_user — P1
  [POST]   /api/users → create_user — P1
  [PUT]    /api/users/{id} → update_user — P1
  [DELETE] /api/users/{id} → delete_user — P1
```

## Notes

- FastAPI auto-generates `/docs` (Swagger UI) and `/redoc` — useful for verification
- Check if there's an API prefix applied globally (e.g., `app = FastAPI(root_path="/api/v1")`)
- Look for middleware that might affect routing (auth, CORS, rate limiting)
