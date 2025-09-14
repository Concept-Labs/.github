# Concept‑Labs Framework

**A PSR‑first, modular PHP framework featuring the Singularity DI container, a declarative config DSL, an event bus, a modern HTTP stack, and a pragmatic SQL layer (DBC/DBAL).**

> Minimal magic. Maximum clarity. Packages self‑describe their DI wiring so dropping a library into `vendor/` is often all it takes.

---

## Highlights

- **Standards‑based**
  - PSR‑7/15/17 HTTP messages & middleware, PSR‑14 events, PSR‑11 container.
- **Singularity DI**
  - Interface→class mappings are declared in each package’s `concept.json` and auto‑merged at runtime.
- **Declarative HTTP pipeline**
  - Define middleware in `etc/middleware.json` with priorities (and optional before/after constraints).
- **Powerful configuration DSL**
  - JSON with `@import`, `@env`, `${...}` substitutions, and **DotArray/DotQuery** for fast node access.
- **Events & Event Bus**
  - `EventBusMiddleware` boots early and can register listeners from code *or* from config. First event: `EventInit`.
- **Views**
  - `phtmal` (fluent HTML builder) and `phtml` (templates). Use either—or mix—per route/layout.
- **SQL that stays friendly**
  - **DBC** (connection/driver/result) + **DBAL** (dialects, expressions, DML builders). Works with PDO; supports `RETURNING`, `LIMIT/OFFSET`, and upserts.

---

## Repository Layout (typical app)

```
htdocs/
└─ pub/
   └─ http.php         # entrypoint
etc/
├─ default.json        # main config (imports)
├─ middleware.json     # HTTP pipeline
├─ router/
│  └─ route.json       # routes
└─ event-bus.json      # event listeners (optional)
src/                   # your application code
vendor/                # packages incl. Concept‑Labs
```

---

## Installation

> **Requirements:** PHP 8.x, Composer, a web server (Nginx/Apache) or a long‑running runtime (RoadRunner/Swoole).

1) Add Concept‑Labs packages to your project (via Packagist or a private VCS/path repo).  
2) `composer install`  
3) Point your web root to `pub/` (the entrypoint is `pub/http.php`).  
4) Configure `etc/default.json` and friends (examples below).

---

## Configuration DSL

`etc/default.json` aggregates your app config with imports and environment overrides:

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

- `@import` merges files (order matters).
- `@env(NAME:default)` reads environment variables.
- `${VAR}` expands inline placeholders.
- Use DotArray/DotQuery to navigate complex trees (`config->node('path.to.key')`).

---

## Dependency Injection (Singularity)

Each package ships a `concept.json` that declares interface preferences. Your app can override or add its own:

```json
{
  "singularity": {
    "package": {
      "concept-labs/http": {
        "preference": {
          "Concept\Http\AppInterface": { "class": "Concept\Http\App" }
        }
      }
    }
  }
}
```

> Packages “plug in” by publishing their preferences; Singularity composes the effective container at boot.

---

## HTTP Pipeline

Declare middleware in `etc/middleware.json`. Higher priority runs earlier (you can formalize bands like 9000 boot, 7000 security, 5800 routing, 100 terminal).

```json
{
  "concept-labs/event-dispatcher:event-bus": {
    "preference": "Concept\EventDispatcher\Middleware\EventBusMiddleware",
    "priority": 9000
  },
  "concept-labs/http:router": {
    "preference": "Concept\Http\Router\RouterMiddleware",
    "priority": 5800
  },
  "concept-labs/http:flusher": {
    "preference": "Concept\Http\Middleware\ResponseFlusherMiddleware",
    "priority": 100
  }
}
```

**Routes** live in `etc/router/route.json`:

```json
{
  "route": {
    "/":           { "handler": "App\Action\Home" },
    "/users/{id}": { "handler": "App\Action\UserView" }
  }
}
```

---

## Events & Event Bus

Early bootstrap happens through `EventBusMiddleware`. You can add listeners from config (`etc/event-bus.json`) or in code.

```json
{
  "event-bus": {
    "listeners": {
      "Concept\EventDispatcher\Event\Init\EventInit": {
        "App\Listener\BootExtra": { "priority": 1000 }
      }
    }
  }
}
```

```php
// src/App/Listener/BootExtra.php
namespace App\Listener;

use Concept\EventDispatcher\Event\Init\EventInit;

final class BootExtra
{
    public function __invoke(EventInit $e): void
    {
        // Register more listeners, warm caches, emit diagnostics, etc.
    }
}
```

**Tip for long‑running workers (RR/Swoole):** make listener aggregation idempotent and avoid capturing `$this` in closures; capture only the factory and small value objects.

---

## Database Layer (DBC/DBAL)

**DBC**: PDO‑backed connection/driver/result with sane defaults (real prepared statements, assoc fetch).  
**DBAL**: dialect adapters, SQL expressions, and DML builders (select/insert/update/delete).

**DSN config (PostgreSQL example)** — `etc/db.default.json`:

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

**Fluent DML with `RETURNING` & UPSERT:**

```php
$dbal   = $container->get(Concept\DBAL\DmlManagerInterface::class);

$insert = $dbal->insert('users')
               ->values(['email' => ':email', 'name' => ':name'])
               ->bind(['email' => 'a@b.com', 'name' => 'Ann'])
               ->returning('id');

$dbc  = $container->get(Concept\DBC\ConnectionInterface::class);
$id   = (int)$dbc->query((string)$insert, $insert->getBindings())->fetchColumn();

$upsert = $dbal->insert('users')
               ->values(['id' => ':id', 'email' => ':email'])
               ->onConflict(['id'], ['email']); // SET email = EXCLUDED.email
```

---

## Views

**

Use **phtmal** for fluent composition or **phtml** for traditional templates.

```php
use Concept\Phtmal\Html as H;

echo H::div([
  H::h1('Hello'),
  H::p('Phtmal in action')
]);
```

---

## Production Notes

- **Stable priority policy**: document your bands (e.g., 9000 boot, 7000 security, 6000 session, 5800 routing, 100 terminal) and enforce via tests.
- **Idempotent event registration**: protect against duplicate listener registration—important in worker modes.
- **Telemetry**: wrap the pipeline with timing/trace middleware; log slow steps and unexpected re‑entries.
- **Secrets**: keep credentials in env; `@env` loads them at runtime.

---

## Testing

- Router edge cases: trailing slashes, greedy params, method mismatch.
- Session middleware: headers, ID regeneration, `session_status` branches.
- Middleware order: verify numeric priority & before/after constraints (topo‑sort if you add it).
- DBAL builders: bindings integrity and dialect‑specific SQL (Postgres/MySQL).

---

## Roadmap

- Optional **pipeline compiler** (bands + topo‑sort, per‑route stacks, revisioned caching).
- Deeper DBAL dialects (JSON/array types, bulk operations).
- PSR‑3 logging + tracing helpers out‑of‑the‑box.

---

## Contributing

PRs and package additions are welcome. Ship your `concept.json` with clear preferences—Singularity will do the rest.

---

## License

See individual package `composer.json`/LICENSE files. For closed‑source use, ensure compatibility before publishing.
