# Quick Start Guide

Get up and running with Concept-Labs in minutes.

## Requirements

- PHP 8.x
- Composer
- Web server (Nginx/Apache) or long-running runtime (RoadRunner/Swoole)

## Project Structure

```
htdocs/
└─ pub/http.php        # entrypoint
etc/
├─ default.json        # root config (imports)
├─ middleware.json     # HTTP pipeline (priorities/before/after)
├─ router/route.json   # routes
└─ event-bus.json      # event listeners (optional)
src/                   # your code
vendor/                # packages (incl. Concept‑Labs)
```

## Installation Steps

1. **Install packages via Composer** (Packagist/private repo/path)
   ```bash
   composer require concept-labs/simple-http
   composer require concept-labs/config
   composer require concept-labs/singularity
   ```

2. **Point web root to `pub/`**
   Configure your web server to serve from the `pub/` directory

3. **Add/import configs in `etc/`**
   Create your configuration files following the examples below

4. **Start your server** (or RoadRunner worker) and visit `/`

## Example Configuration

### etc/default.json
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

## Next Steps

- [Configuration Guide](./configuration.md) - Learn about the configuration DSL
- [Dependency Injection](./dependency-injection.md) - Understand Singularity DI
- [HTTP Stack](./http-stack.md) - Build your HTTP application
- [Architecture Overview](./architecture.md) - Deep dive into the system design
