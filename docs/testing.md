# Testing Guide

Best practices for testing Concept-Labs applications.

## Overview

Concept-Labs is designed with testability in mind. Dependency injection, interface-based design, and minimal global state make testing straightforward.

## Test Organization

```
tests/
├── Unit/              # Unit tests for individual components
├── Integration/       # Tests involving multiple components
└── Feature/          # End-to-end feature tests
```

## Unit Testing

### Testing with Dependency Injection

```php
use PHPUnit\Framework\TestCase;

class UserServiceTest extends TestCase
{
    public function testRegisterUser(): void
    {
        // Mock dependencies
        $repository = $this->createMock(UserRepositoryInterface::class);
        $dispatcher = $this->createMock(EventDispatcherInterface::class);
        
        // Set expectations
        $repository->expects($this->once())
            ->method('save')
            ->with($this->isInstanceOf(User::class));
        
        $dispatcher->expects($this->once())
            ->method('dispatch')
            ->with($this->isInstanceOf(UserRegistered::class));
        
        // Test
        $service = new UserService($repository, $dispatcher);
        $user = $service->registerUser('test@example.com', 'password');
        
        $this->assertInstanceOf(User::class, $user);
    }
}
```

### Testing Router Edge Cases

- **Trailing slashes** - Ensure routes handle with/without trailing slashes
- **Greedy params** - Test parameter matching doesn't capture too much
- **Method mismatch** - Verify proper 405 responses
- **Not found** - Verify proper 404 responses

### Testing Middleware Order

Enforce bands + before/after constraints:

```php
public function testMiddlewareOrder(): void
{
    $pipeline = $this->buildPipeline();
    $middlewares = $pipeline->getMiddlewares();
    
    // Assert security runs before routing
    $this->assertLessThan(
        $this->findPriority($middlewares, RouterMiddleware::class),
        $this->findPriority($middlewares, AuthMiddleware::class)
    );
}
```

### Testing Session Middleware

- Cache headers
- ID regeneration
- `session_status` branches

### Testing DBAL

Verify bindings, SQL emitted per dialect, and `RETURNING`/upsert paths:

```php
public function testInsertWithReturning(): void
{
    $insert = $this->dbal->insert('users')
                         ->values(['email' => ':email'])
                         ->bind(['email' => 'test@example.com'])
                         ->returning('id');
    
    $sql = (string)$insert;
    $this->assertStringContainsString('RETURNING', $sql);
    $this->assertEquals(['email' => 'test@example.com'], $insert->getBindings());
}
```

### Testing EventBus

Idempotent aggregation and listener error isolation:

```php
public function testEventBusIdempotency(): void
{
    $eventBus = new EventBus($config);
    $eventBus->aggregate();
    $countFirst = $eventBus->getListenerCount(UserRegistered::class);
    
    $eventBus->aggregate();
    $countSecond = $eventBus->getListenerCount(UserRegistered::class);
    
    $this->assertEquals($countFirst, $countSecond);
}
```

## Integration Testing

### Testing HTTP Stack

```php
use Psr\Http\Message\ServerRequestInterface;

class HttpIntegrationTest extends TestCase
{
    public function testFullRequestCycle(): void
    {
        $app = $this->createApplication();
        
        $request = $this->createServerRequest('GET', '/users/123');
        $response = $app->handle($request);
        
        $this->assertEquals(200, $response->getStatusCode());
        $this->assertJson($response->getBody());
    }
}
```

### Testing with Database

```php
class DatabaseIntegrationTest extends TestCase
{
    protected function setUp(): void
    {
        $this->connection = $this->createTestDatabase();
        $this->connection->beginTransaction();
    }
    
    protected function tearDown(): void
    {
        $this->connection->rollBack();
    }
    
    public function testUserRepository(): void
    {
        $repository = new UserRepository($this->connection);
        $user = $repository->create(['email' => 'test@example.com']);
        
        $found = $repository->findById($user->getId());
        $this->assertEquals($user->getEmail(), $found->getEmail());
    }
}
```

## Test Configuration

### phpunit.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit bootstrap="vendor/autoload.php" colors="true">
    <testsuites>
        <testsuite name="unit">
            <directory>tests/Unit</directory>
        </testsuite>
        <testsuite name="integration">
            <directory>tests/Integration</directory>
        </testsuite>
        <testsuite name="feature">
            <directory>tests/Feature</directory>
        </testsuite>
    </testsuites>
    
    <coverage>
        <include>
            <directory>src</directory>
        </include>
    </coverage>
</phpunit>
```

## Best Practices

1. **Test behavior, not implementation** - Focus on public APIs
2. **Use dependency injection** - Makes mocking easy
3. **Isolate tests** - Each test should be independent
4. **Keep tests fast** - Unit tests should run in milliseconds
5. **Use descriptive names** - Test names should describe what they test
6. **Follow AAA pattern** - Arrange, Act, Assert
7. **Mock external dependencies** - Don't make real HTTP calls or database queries in unit tests
8. **Use test databases** - Use transactions that rollback for integration tests

## Running Tests

```bash
# Run all tests
vendor/bin/phpunit

# Run specific suite
vendor/bin/phpunit --testsuite unit

# Run with coverage
vendor/bin/phpunit --coverage-html coverage

# Run specific test
vendor/bin/phpunit tests/Unit/UserServiceTest.php
```

## Next Steps

- [Debugging](./debugging.md) - Debugging applications
- [Performance](./performance.md) - Performance testing
- [CI/CD](./ci-cd.md) - Continuous integration setup
