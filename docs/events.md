# Events & Event Bus

Application-wide event coordination with PSR-14 Event Dispatcher.

## Overview

The Concept-Labs event system provides PSR-14 compliant event dispatching with an EventBus for coordinating application-wide events.

## EventBusMiddleware

**EventBusMiddleware** aggregates listeners from config and/or code, fires an early `EventInit`, and hands off to the next middleware.

## Configuration

### Config-driven listeners

Define listeners in `etc/event-bus.json`:

```json
{
  "event-bus": {
    "listeners": {
      "Concept\\EventDispatcher\\Event\\Init\\EventInit": {
        "App\\Listener\\BootExtra": { "priority": 1000 }
      },
      "App\\Event\\UserRegistered": {
        "App\\Listener\\SendWelcomeEmail": { "priority": 100 },
        "App\\Listener\\UpdateAnalytics": { "priority": 50 }
      }
    }
  }
}
```

## Creating Events

Events are simple value objects:

```php
namespace App\Event;

class UserRegistered
{
    public function __construct(
        public readonly int $userId,
        public readonly string $email,
        public readonly \DateTimeInterface $registeredAt
    ) {}
}
```

## Creating Listeners

Listeners can be classes or callables:

```php
namespace App\Listener;

use App\Event\UserRegistered;

class SendWelcomeEmail
{
    public function __construct(
        private MailerInterface $mailer
    ) {}
    
    public function __invoke(UserRegistered $event): void
    {
        $this->mailer->send(
            to: $event->email,
            template: 'welcome'
        );
    }
}
```

## Dispatching Events

```php
// Inject the dispatcher
public function __construct(
    private EventDispatcherInterface $dispatcher
) {}

// Dispatch an event
$event = new UserRegistered($userId, $email, new \DateTimeImmutable());
$this->dispatcher->dispatch($event);
```

## Best Practices

### Idempotency
In long-running workers, ensure aggregating listeners is idempotent (e.g., cache a revision hash; avoid re-registering duplicates).

### Listener Closures
Capture only the **factory** and small config arrays; avoid capturing `$this` to prevent accidental memory retention across requests.

```php
// ❌ Bad: captures $this
$dispatcher->addListener(UserRegistered::class, function($event) {
    $this->doSomething($event); // Memory leak!
});

// ✅ Good: captures only what's needed
$factory = $this->getFactory();
$dispatcher->addListener(UserRegistered::class, function($event) use ($factory) {
    $service = $factory->create();
    $service->doSomething($event);
});
```

### Error Isolation
Wrap listener invocation to log exceptions without crashing the pipeline. Use PSR-3 where available.

```php
try {
    $listener($event);
} catch (\Throwable $e) {
    $this->logger->error('Listener failed', [
        'event' => get_class($event),
        'listener' => get_class($listener),
        'exception' => $e
    ]);
}
```

## Stoppable Events

Implement `StoppableEventInterface` to allow listeners to prevent further propagation:

```php
use Psr\EventDispatcher\StoppableEventInterface;

class ProcessableEvent implements StoppableEventInterface
{
    private bool $stopped = false;
    
    public function stopPropagation(): void
    {
        $this->stopped = true;
    }
    
    public function isPropagationStopped(): bool
    {
        return $this->stopped;
    }
}
```

## Testing

Testing event-driven code:

```php
// Mock the dispatcher
$dispatcher = $this->createMock(EventDispatcherInterface::class);
$dispatcher->expects($this->once())
    ->method('dispatch')
    ->with($this->isInstanceOf(UserRegistered::class));

// Inject and test
$service = new UserService($dispatcher);
$service->registerUser($email, $password);
```

## Next Steps

- [Middleware Pipeline](./http-stack.md) - Integrate with HTTP stack
- [Testing Guide](./testing.md) - Testing event-driven code
- [Advanced Patterns](./advanced-patterns.md) - Complex event patterns

## Related Resources

- [event-dispatcher package](https://github.com/Concept-Labs/event-dispatcher) - PSR-14 implementation
