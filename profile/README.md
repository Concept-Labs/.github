# Concept‑Labs Simple Framework

**A PSR‑first, modular PHP framework featuring the Singularity DI container, a declarative config DSL, an event bus, a modern HTTP stack, and a pragmatic SQL layer (DBC/DBAL).**

> Minimal magic. Maximum clarity. Packages self‑describe their DI wiring, configuration, and middleware so “drop‑in” integration is the norm.

---

## Table of Contents

1. [Philosophy](#philosophy)
2. [Quick Start](#quick-start)
3. [Architecture Overview](#architecture-overview)
4. [Configuration DSL](#configuration-dsl)
5. [Dependency Injection (Singularity)](#dependency-injection-singularity)
6. [HTTP Stack & Middleware Pipeline](#http-stack--middleware-pipeline)
7. [Events & Event Bus](#events--event-bus)
8. [Database Layer (DBC/DBAL)](#database-layer-dbcd bal)
9. [Views: phtmal & phtml](#views-phtmal--phtml)
10. [Error Handling & Problem Details](#error-handling--problem-details)
11. [Security Notes](#security-notes)
12. [Performance & Long‑Running Runtimes](#performance--longrunning-runtimes)
13. [Testing Guidance](#testing-guidance)
14. [Project Layout & Conventions](#project-layout--conventions)
15. [Roadmap](#roadmap)
16. [Contributing](#contributing)
17. [License](#license)

---

## Philosophy

- **PSR‑aligned**: PSR‑7/15/17 for HTTP, PSR‑14 for events, PSR‑11 for DI.\
- **Explicit over implicit**: behavior is controlled by JSON configs and DI preferences you can read and diff.\
- **Composable**: each package brings its own `concept.json`, optional middleware, and config. The app chooses what to include.\
- **Production‑minded**: guard rails for RoadRunner/Swoole workers, low‑overhead middleware, and clear error boundaries.\
- **Testable**: builders and adapters are designed for isolation, with minimal singletons and no global state.

---

## Quick Start

**Requirements:** PHP 8.x, Composer, web server (Nginx/Apache) or long‑runner (RoadRunner/Swoole).

```
htdocs/
└─ pub/http.php        # entrypoint
etc/
├─ default.json        # root config (imports)
├─ middleware.json     # HTTP pipeline (priorities/before/after) - as an example: @import(middleware.json) from default.json
├─ router/route.json   # routes
└─ event-bus.json      # event listeners (optional)
src/                   # your code
vendor/                # packages (incl. Concept‑Labs)
```

1) Install packages via Composer (Packagist/private repo/path).\
2) Point web root to `pub/`.\
3) Add/import configs in `etc/` (see below).\
4) Start your server (or RR worker) and visit `/`.

---

## Architecture Overview

```
            ┌─────────────┐
Request  →  │  Middleware │  (pipeline: ...security... → ...session... → ...router... →... flusher)
            └──────┬──────┘
                   │        PSR‑15
                   ▼
            ┌─────────────┐
            │   Router    │  → handler/controller
            └──────┬──────┘
                   │
                   ▼
            ┌─────────────┐
            │   Views     │  phtmal (fluent) / phtml (templates) / pure html + js
            └─────────────┘

Events: PSR‑14 dispatcher + EventBus; early boot via EventBusMiddleware.
Config: JSON DSL (imports, env, placeholders) + DotArray/DotQuery.
DI:    Singularity (interface preferences declared in concept.json).
DB:    DBC (PDO drivers) + DBAL (dialects, DML builders, expressions).
```

---

## Configuration DSL

Configuration is JSON with a small DSL that’s resolved at boot.

**Directives & Expressions**

- `@import`: include/merge other JSON files in order (last wins on conflicts).
- `@env(NAME:default)`: read environment variables with a default fallback.
- `${path.to.value}`: inline substitution from the current config tree.

**Resolution Order (nuance):**

1. Load root (e.g., `etc/default.json`).\
2. Expand `@import` in order; merge **shallow maps** by key, **append arrays** unless explicitly documented otherwise in a given node.\
3. Evaluate `@env`.\
4. Apply `${...}` substitutions **after** all imports/env are present.\
5. Some packages may declare `extra.concept.include` (or similar) in `composer.json` to be picked up automatically by the app’s bootstrap (if you enable it).

**Escaping substitutions:** write `\${...}` to render a literal `${...}`.\
**Dot access:** via DotArray/DotQuery (e.g., `config->node('dbc.connection.dsn')`).

Example `etc/default.json`:
```json
{
  "@import": [
    "etc/middleware.json",
    "etc/router/route.json",
    "etc/event-bus.json",
    "etc/db.default.json"
  ]
}
```

---

## Dependency Injection (Singularity)

Each package ships a `concept.json` with its preferences. The app can add/override its own.

**Package‑level (inside vendor package):**
```json
{
  "singularity": {
    "package": {
      "concept-labs/http": {
        "preference": {
          "Concept\\Http\\AppInterface": { "class": "Concept\\Http\\App" }
        }
      }
    }
  }
}
```

**App‑level overrides (your repo):**
```json
{
  "singularity": {
    "package": {
      "app/http": {
        "preference": {
          "Concept\\Http\\AppInterface": { "class": "App\\Http\\CustomApp" }
        }
      }
    }
  }
}
```

**Nuances & Best Practices**

- **Determinism**: if two packages bind the same interface, the app’s own preferences should win (policy is in development/ alternatively filesystem sort is used).\
- **Factories**: prefer small factories over heavy constructors; wire factories through DI for listeners, handlers, and middleware.\
- **Scoping**: in long‑running workers, avoid capturing container in closures; resolve per request or pass small value objects.\
- **Discoverability**: keep preferences close to the package they belong to—this improves self‑documentation.

---

## HTTP Stack & Middleware Pipeline

Declare middleware in `etc/middleware.json`.\
**Rule of thumb**: higher priority runs earlier. Reserve “bands” to keep a stable mental model:

- 9000–8000: **boot** (feature flags, event bus init)\
- 7900–7000: **security** (CORS, auth, CSRF, rate‑limit)\
- 6900–6000: **infra** (session, locale, body parsers)\
- 5900–5000: **routing**\
- 4900–4000: handler adapters\
- 3900–3000: response transformers (templating, normalizers)\
- 1100–1000: telemetry (logging/metrics)\
- ≤ 100: terminal (404, throwable boundary, flusher)

```json
{
  "concept-labs/event-dispatcher:event-bus": {
    "preference": "Concept\\EventDispatcher\\Middleware\\EventBusMiddleware",
    "priority": 9000
  },
  "concept-labs/http:router": {
    "preference": "Concept\\Http\\Router\\RouterMiddleware",
    "priority": 5800
  },
  "concept-labs/http:flusher": {
    "preference": "Concept\\Http\\Middleware\\ResponseFlusherMiddleware",
    "priority": 100
  }
}
```

---

## Events & Event Bus

**EventBusMiddleware** aggregates listeners from config and/or code, fires an early `EventInit`, and hands off to the next middleware.

**Config‑driven listeners (`etc/event-bus.json`)**

```json
{
  "event-bus": {
    "listeners": {
      "Concept\\EventDispatcher\\Event\\Init\\EventInit": {
        "App\\Listener\\BootExtra": { "priority": 1000 }
      },
      "App\\Event\\UserRegistered": {
        "App\\Listener\\SendWelcomeEmail": { "priority": 100 }
      }
    }
  }
}
```

**Idempotency (nuance)**\
In long‑running workers, ensure aggregating listeners is idempotent (e.g., cache a revision hash; avoid re‑registering duplicates).

**Listener closures (nuance)**\
Capture only the **factory** and small config arrays; avoid capturing `$this` to prevent accidental memory retention across requests.

**Error isolation**\
Wrap listener invocation to log exceptions without crashing the pipeline. Use PSR‑3 where available.

---

## Database Layer (DBC/DBAL)

**DBC** (PDO‑backed connection/driver/result) gives you predictable behavior:\
- real prepared statements (`ATTR_EMULATE_PREPARES=false` by default),\
- assoc fetch mode,\
- simple `query/prepare/exec/begin/commit/rollBack/lastInsertId` API.

**DBAL** provides:\
- dialect adapters (quoting identifiers, `LIMIT/OFFSET`, `RETURNING`, conflict resolution),\
- SQL expressions, builders for `select/insert/update/delete`,\
- binding management (`bind([...])`), and optional `returning()` / `onConflict()` helpers.

**DSN (PostgreSQL example)** — `etc/db.default.json`

```json
{
  "dbc": {
    "connection": {
      "dsn": "pgsql:host=${ENV.DB_HOST:127.0.0.1};port=${ENV.DB_PORT:5432};dbname=${ENV.DB_NAME:app}",
      "username": "@env(DB_USER:app)",
      "password": "@env(DB_PASS:secret)",
      "options": {
        "ATTR_ERRMODE": "ERRMODE_EXCEPTION",
        "ATTR_DEFAULT_FETCH_MODE": "FETCH_ASSOC",
        "ATTR_EMULATE_PREPARES": false
      }
    }
  }
}
```

**Fluent DML**

```php
$dbal = $container->get(Concept\DBAL\DmlManagerInterface::class);

$insert = $dbal->insert('users')
               ->values(['email' => ':email', 'name' => ':name'])
               ->bind(['email' => 'a@b.com', 'name' => 'Ann'])
               ->returning('id'); // Postgres

$dbc  = $container->get(Concept\DBC\ConnectionInterface::class);
$id   = (int)$dbc->query((string)$insert, $insert->getBindings())->fetchColumn();

$select = $dbal->select('users')->where('id = :id')->bind(['id' => $id]);
$user   = $dbc->query((string)$select, $select->getBindings())->fetch();
```

**Dialect nuances**

- **Postgres**
  - Prefer `RETURNING` over `lastInsertId()`. If you call `lastInsertId`, pass the sequence name.\
  - Identifiers quoted as `"schema"."table"`; arrays supported via `= ANY(:ids)`.
- **MySQL/MariaDB**
  - Use `LAST_INSERT_ID()` or PDO `lastInsertId()` (no `RETURNING` in MySQL < 8.0.27; MariaDB differs).\
  - Upsert via `INSERT ... ON DUPLICATE KEY UPDATE`.\
  - Backticks for quoting identifiers (handled by the dialect).

**Transactions**

```php
$dbc->beginTransaction();
try {
    $dbc->query((string)$insert, $insert->getBindings());
    $dbc->commit();
} catch (\Throwable $e) {
    $dbc->rollBack();
    throw $e;
}
```

**Binding types (nuance)**\
Bind scalars with appropriate PDO types (int/bool/null/string). For bulk arrays, expand to `:i1,:i2,...` placeholders or use dialect helpers.

---

## Views: phtmal & phtml

- **phtmal** (fluent builder): ergonomic for componentized UIs.\
- **phtml** (templates): flexible for classic server‑side rendering.\
- You can mix them—e.g., phtmal for components embedded into a phtml layout.

**XSS safety**: escape untrusted content; prefer dedicated helpers or strict auto‑escape in templates.

---

## Error Handling & Problem Details

Centralize errors in a **single boundary middleware** that:\
- converts exceptions into either HTML or JSON (content negotiation),\
- emits consistent payloads (consider RFC 7807 Problem Details),\
- logs via PSR‑3 with request correlation IDs.

Keep **404/405** and **flusher** as terminal middleware with very low priority (execute last).

---

## Security Notes

- **CORS** & **CSRF**: provide dedicated middleware in the security band (7xxx).\
- **Auth**: short‑circuit early when unauthenticated; keep handlers unaware of transport details.\
- **Headers**: set `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy`, `Strict-Transport-Security`.\
- **Secrets**: never hardcode; load via `@env`. Consider dotenv only for local dev.\
- **Sessions**: regenerate IDs on privilege change; set `SameSite`, `HttpOnly`, `Secure` appropriately.

---

## Performance & Long‑Running Runtimes

- **Compile pipelines** once (optional topo‑sort) and cache with a `revisionId` (hash of config).\
- **Avoid capturing `$this`** in request‑lifetime closures; capture factories/value arrays instead.\
- **Idempotent setup**: ensure event aggregation and container boot are guarded in workers.\
- **Metrics**: measure per‑middleware latency (µs) and surface `/__debug/mw` (dev only) to dump the ordered stack.

OPcache: enable and warm; keep files immutable between releases to maximize cache hits.

---

## Testing Guidance

- **Router edge cases**: trailing slashes, greedy params, method mismatch.\
- **Middleware order**: enforce bands + before/after constraints in tests.\
- **Session middleware**: cache headers, ID regeneration, `session_status` branches.\
- **DBAL**: verify bindings, SQL emitted per dialect, and `RETURNING`/upsert paths.\
- **EventBus**: idempotent aggregation and listener error isolation.

Sample `phpunit.xml` (excerpt):
```xml
<phpunit bootstrap=\"vendor/autoload.php\" colors=\"true\">
  <testsuites>
    <testsuite name=\"unit\">
      <directory>tests/Unit</directory>
    </testsuite>
    <testsuite name=\"integration\">
      <directory>tests/Integration</directory>
    </testsuite>
  </testsuites>
</phpunit>
```

---

## Project Layout & Conventions

- **App code** in `src/`; avoid fat static helpers—prefer small services via DI.\
- **Configs** in `etc/`; keep secrets out of VCS. Provide `*.dist` templates.\
- **Packages**: each has its own `composer.json` + `concept.json`. Keep public APIs `final` only when stability requires it; prefer interfaces for extension seams.\
- **Coding standards**: PSR‑12, PHP‑CS‑Fixer; static analysis with PHPStan (level per package), CI matrices for multi‑package repos.

Versioning: Semantic Versioning (SemVer) across packages; document BC breaks and provide migration notes.

---

## Roadmap

- Optional **pipeline compiler**: bands + topo‑sort, per‑route stacks, revisioned caching, debug dumping.\
- DBAL: richer Postgres/MySQL dialects (JSON/JSONB, array binds, bulk operations).\
- Out‑of‑the‑box PSR‑3 logging + tracing bridges.
  
---

## Contributing

PRs and package additions are welcome. Ship your `concept.json` with clear DI preferences and—if applicable—middleware declarations. Keep changes small and well‑tested.

---

## License

See individual package `composer.json`/LICENSE files. For closed‑source use, ensure license compatibility before publishing.
