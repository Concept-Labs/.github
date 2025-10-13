# Dependency Injection (Singularity)

Complete guide to the Singularity DI container.

## Overview

Each package ships a `concept.json` with its preferences. The app can add/override its own. Singularity is a PSR-11 compliant dependency injection container designed for clarity and flexibility.

## Configuration

### Package-level (inside vendor package)

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

### App-level overrides (your repo)

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

## Key Concepts

### Interface Preferences
Bind interfaces to concrete implementations:

```php
// The container resolves interfaces based on preferences
$app = $container->get(AppInterface::class);
// Returns instance of Concept\Http\App or App\Http\CustomApp
```

### Lifecycle Patterns

- **Transient** - New instance every time
- **Shared** (Singleton) - One instance across the application
- **Weak** - Shared but can be garbage collected
- **Prototype** - Cloned from a prototype instance

### Autowiring

The container can automatically resolve dependencies:

```php
class UserController {
    public function __construct(
        private UserRepository $users,
        private Logger $logger
    ) {}
}

// Container automatically resolves dependencies
$controller = $container->get(UserController::class);
```

## Best Practices

### Determinism
If two packages bind the same interface, the app's own preferences should win (policy is in development/ alternatively filesystem sort is used).

### Factories
Prefer small factories over heavy constructors; wire factories through DI for listeners, handlers, and middleware.

```php
class ControllerFactory {
    public function create(): ControllerInterface {
        return new Controller(/* dependencies */);
    }
}
```

### Scoping
In long-running workers, avoid capturing container in closures; resolve per request or pass small value objects.

### Discoverability
Keep preferences close to the package they belong to—this improves self-documentation.

## Advanced Features

### Method Injection
Services can implement `InjectableInterface` for automatic method injection:

```php
class Service implements InjectableInterface {
    public function inject(LoggerInterface $logger): void {
        $this->logger = $logger;
    }
}
```

### Lazy Loading
Use `LazyGhostInterface` for services that should only be instantiated when used:

```php
class ExpensiveService implements LazyGhostInterface {
    // Only created when methods are called
}
```

### Plugin System
Extend container behavior with plugins:

```php
$container->registerPlugin(new CustomPlugin());
```

## Testing

The container makes testing easy:

```php
// Override services for testing
$container->set(ApiClientInterface::class, new MockApiClient());

// Test with real dependencies
$service = $container->get(ServiceToTest::class);
```

## Next Steps

- [Configuration](./configuration.md) - Configure the container
- [Advanced Patterns](./advanced-patterns.md) - Complex DI scenarios
- [Testing Guide](./testing.md) - Testing with DI

## Related Resources

- [singularity package](https://github.com/Concept-Labs/singularity) - Complete DI documentation
