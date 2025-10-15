# Події та EventBus

Координація подій у всьому додатку з PSR-14 Event Dispatcher.

## Огляд

Система подій Concept-Labs надає PSR-14 сумісну диспетчеризацію подій з EventBus для координації подій всього додатка.

## EventBusMiddleware

**EventBusMiddleware** агрегує слухачів з конфігурації та/або коду, запускає ранню `EventInit`, і передає керування наступному middleware.

## Конфігурація

### Слухачі на основі конфігурації

Визначте слухачів у `etc/event-bus.json`:

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

## Створення подій

Події — це прості об'єкти-значення:

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

## Створення слухачів

Слухачі можуть бути класами або викликуваними:

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

## Диспетчеризація подій

```php
// Впровадіть диспетчер
public function __construct(
    private EventDispatcherInterface $dispatcher
) {}

// Відправте подію
$event = new UserRegistered($userId, $email, new \DateTimeImmutable());
$this->dispatcher->dispatch($event);
```

## Найкращі практики

### Ідемпотентність
У довготривалих воркерах переконайтеся, що агрегація слухачів ідемпотентна (наприклад, кешуйте хеш ревізії; уникайте повторної реєстрації дублікатів).

### Замикання слухачів
Захоплюйте лише **фабрику** та невеликі масиви конфігурації; уникайте захоплення `$this`, щоб запобігти випадковому утриманню пам'яті між запитами.

```php
// ❌ Погано: захоплює $this
$dispatcher->addListener(UserRegistered::class, function($event) {
    $this->doSomething($event); // Витік пам'яті!
});

// ✅ Добре: захоплює лише те, що потрібно
$factory = $this->getFactory();
$dispatcher->addListener(UserRegistered::class, function($event) use ($factory) {
    $service = $factory->create();
    $service->doSomething($event);
});
```

### Ізоляція помилок
Обгорніть виклик слухача, щоб логувати винятки без збою конвеєра. Використовуйте PSR-3, де доступно.

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

## Зупинні події

Реалізуйте `StoppableEventInterface`, щоб дозволити слухачам запобігти подальшому розповсюдженню:

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

## Тестування

Тестування коду, керованого подіями:

```php
// Мокуйте диспетчер
$dispatcher = $this->createMock(EventDispatcherInterface::class);
$dispatcher->expects($this->once())
    ->method('dispatch')
    ->with($this->isInstanceOf(UserRegistered::class));

// Впровадіть та тестуйте
$service = new UserService($dispatcher);
$service->registerUser($email, $password);
```

## Наступні кроки

- [Конвеєр Middleware](./http-stack.md) - Інтеграція з HTTP стеком
- [Посібник з тестування](./testing.md) - Тестування коду, керованого подіями
- [Розширені патерни](./advanced-patterns.md) - Складні патерни подій

## Пов'язані ресурси

- [пакет event-dispatcher](https://github.com/Concept-Labs/event-dispatcher) - Реалізація PSR-14
