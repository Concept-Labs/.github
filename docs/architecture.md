# Architecture Overview

Understanding the Concept-Labs ecosystem architecture.

## System Architecture

```
            ┌─────────────┐
Request  →  │  Middleware │  (pipeline: ...security... → ...session... → ...router... →... flusher)
            └──────┬──────┘
                   │        PSR‑15
                   ▼
            ┌─────────────┐
            │   Router    │  → handler/controller
            └──────┬──────┘
                   ▼
            ┌─────────────┐
            | ?Middleware?|
            └──────┬──────┘
                   │
                   ▼
     ┌─────────────┐┌─────────────┐
     │   Raw/API   ││   Views     │
     └─────────────┘└─────────────┘

Events: PSR‑14 dispatcher + EventBus; early boot via EventBusMiddleware.
Config: JSON DSL (imports, env, placeholders) + DotArray/DotQuery.
DI:    Singularity (interface preferences declared in concept.json).
DB:    DBC (PDO drivers) + DBAL (dialects, DML builders, expressions).
```

## Core Components

### Configuration Layer
- **JSON DSL** with imports, environment variables, and placeholder substitution
- **DotArray/DotQuery** for intuitive nested access
- **Package-level configuration** via `concept.json`

### Dependency Injection (Singularity)
- **PSR-11 compliant** container
- **Interface preferences** for flexible service binding
- **Package-level DI configuration** for modular architecture
- **Multiple lifecycle patterns** (Transient, Shared, Weak, Prototype)

### HTTP Stack
- **PSR-7/15/17** for HTTP messages, middleware, and factories
- **Priority-based middleware pipeline** with configurable bands
- **Router** with flexible routing capabilities
- **Middleware bands** for organized request processing

### Event System
- **PSR-14 Event Dispatcher** for standardized event handling
- **EventBus** for application-wide event coordination
- **Config-driven listeners** for declarative event handling

### Database Layer
- **DBC** (Database Connectivity) for PDO-based connections
- **DBAL** (Database Abstraction Layer) for dialect-specific queries
- **Fluent query builders** for type-safe database operations
- **Support for PostgreSQL, MySQL/MariaDB** with dialect adapters

## Design Principles

### PSR Standards First
All components follow PHP-FIG standards for maximum interoperability:
- PSR-3 (Logging), PSR-7 (HTTP Messages), PSR-11 (Container)
- PSR-14 (Event Dispatcher), PSR-15 (HTTP Handlers)
- PSR-17 (HTTP Factories), PSR-18 (HTTP Client)

### Modular & Composable
- Each package is self-contained with its own `concept.json`
- Packages declare their dependencies and preferences
- Applications choose which packages to include

### Production-Ready
- Built for long-running processes (RoadRunner, Swoole)
- Low-overhead middleware pipeline
- Clear error boundaries and exception handling
- Memory-efficient design patterns

### Explicit Configuration
- Behavior controlled by readable JSON configs
- No hidden magic or implicit conventions
- Configuration can be version-controlled and diffed

## Next Steps

- [Configuration DSL](./configuration.md) - Learn the configuration system
- [Dependency Injection](./dependency-injection.md) - Understand the DI container
- [HTTP Stack](./http-stack.md) - Build HTTP applications
- [Database Layer](./database.md) - Work with databases
