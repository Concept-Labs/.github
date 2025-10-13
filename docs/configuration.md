# Configuration DSL

Complete guide to Concept-Labs configuration system.

## Overview

Configuration is JSON with a small DSL that's resolved at boot. The system provides a powerful yet simple way to manage application settings across environments.

## Directives & Expressions

### @import
Include and merge other JSON files in order (last wins on conflicts).

```json
{
  "@import": [
    "etc/middleware.json",
    "etc/router/route.json",
    "etc/event-bus.json"
  ]
}
```

### @env(NAME:default)
Read environment variables with a default fallback.

```json
{
  "database": {
    "host": "@env(DB_HOST:localhost)",
    "username": "@env(DB_USER:root)",
    "password": "@env(DB_PASS:secret)"
  }
}
```

### ${path.to.value}
Inline substitution from the current config tree.

```json
{
  "paths": {
    "root": "/var/www",
    "public": "${paths.root}/public",
    "storage": "${paths.root}/storage"
  }
}
```

## Resolution Order

Understanding how configuration is processed:

1. **Load root** (e.g., `etc/default.json`)
2. **Expand @import** in order; merge shallow maps by key, append arrays unless explicitly documented otherwise in a given node
3. **Evaluate @env** directives
4. **Apply ${...}** substitutions after all imports/env are present
5. **Process package configs** - Some packages may declare `extra.concept.include` (or similar) in `composer.json` to be picked up automatically by the app's bootstrap (if you enable it)

## Advanced Features

### Escaping Substitutions
Write `\${...}` to render a literal `${...}`.

### Dot Access
Via DotArray/DotQuery (e.g., `config->node('dbc.connection.dsn')`).

```php
$config->get('database.host');
$config->set('cache.driver', 'redis');
$config->has('app.debug');
```

## Package Configuration

Each package can provide its own `concept.json` for self-contained configuration:

```json
{
  "singularity": {
    "package": {
      "concept-labs/http": {
        "preference": {
          "Concept\\Http\\AppInterface": { 
            "class": "Concept\\Http\\App" 
          }
        }
      }
    }
  }
}
```

## Example Configurations

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

### etc/db.default.json (PostgreSQL)
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

## Best Practices

1. **Keep secrets out of VCS** - Use environment variables for sensitive data
2. **Provide .dist templates** - Include example configurations
3. **Use package-level config** - Keep configuration close to the code that uses it
4. **Document custom directives** - If you add custom DSL features, document them
5. **Version control configs** - Track configuration changes alongside code

## Next Steps

- [Dependency Injection](./dependency-injection.md) - Configure the DI container
- [HTTP Stack](./http-stack.md) - Configure middleware and routing
- [Event System](./events.md) - Configure event listeners
- [Database Layer](./database.md) - Configure database connections

## Related Resources

- [concept-labs/config package](https://github.com/Concept-Labs/config) - Full configuration documentation
