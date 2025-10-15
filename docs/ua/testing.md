# Посібник з тестування

Найкращі практики для тестування додатків Concept-Labs.

## Огляд

Concept-Labs розроблений з урахуванням тестованості. Впровадження залежностей, дизайн на основі інтерфейсів та мінімальний глобальний стан роблять тестування простим.

## Організація тестів

```
tests/
├── Unit/              # Юніт-тести для окремих компонентів
├── Integration/       # Тести, що включають декілька компонентів
└── Feature/          # Наскрізні тести функцій
```

## Юніт-тестування

### Тестування з впровадженням залежностей

```php
use PHPUnit\Framework\TestCase;

class UserServiceTest extends TestCase
{
    public function testRegisterUser(): void
    {
        // Мокуємо залежності
        $repository = $this->createMock(UserRepositoryInterface::class);
        $dispatcher = $this->createMock(EventDispatcherInterface::class);
        
        // Встановлюємо очікування
        $repository->expects($this->once())
            ->method('save')
            ->with($this->isInstanceOf(User::class));
        
        $dispatcher->expects($this->once())
            ->method('dispatch')
            ->with($this->isInstanceOf(UserRegistered::class));
        
        // Тестуємо
        $service = new UserService($repository, $dispatcher);
        $user = $service->registerUser('test@example.com', 'password');
        
        $this->assertInstanceOf(User::class, $user);
    }
}
```

### Тестування граничних випадків Router

- **Кінцеві слеші** - Переконайтеся, що маршрути обробляють з/без кінцевих слешів
- **Жадібні параметри** - Тестуйте, що відповідність параметрів не захоплює занадто багато
- **Невідповідність методу** - Перевірте правильні відповіді 405
- **Не знайдено** - Перевірте правильні відповіді 404

### Тестування порядку Middleware

Забезпечте смуги + обмеження before/after:

```php
public function testMiddlewareOrder(): void
{
    $pipeline = $this->buildPipeline();
    $middlewares = $pipeline->getMiddlewares();
    
    // Перевірте, що безпека виконується перед маршрутизацією
    $this->assertLessThan(
        $this->findPriority($middlewares, RouterMiddleware::class),
        $this->findPriority($middlewares, AuthMiddleware::class)
    );
}
```

### Тестування Session Middleware

- Заголовки кешу
- Регенерація ID
- Гілки `session_status`

### Тестування DBAL

Перевірте зв'язування, SQL, згенерований для кожного діалекту, та шляхи `RETURNING`/upsert:

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

### Тестування EventBus

Ідемпотентна агрегація та ізоляція помилок слухачів:

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

## Інтеграційне тестування

### Тестування HTTP стеку

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

### Тестування з базою даних

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

## Конфігурація тестів

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

## Найкращі практики

1. **Тестуйте поведінку, а не реалізацію** - Фокусуйтеся на публічних API
2. **Використовуйте впровадження залежностей** - Робить мокування легким
3. **Ізолюйте тести** - Кожен тест повинен бути незалежним
4. **Тримайте тести швидкими** - Юніт-тести повинні виконуватися за мілісекунди
5. **Використовуйте описові імена** - Імена тестів повинні описувати, що вони тестують
6. **Дотримуйтесь патерну AAA** - Arrange, Act, Assert (Організувати, Діяти, Перевірити)
7. **Мокуйте зовнішні залежності** - Не робіть справжніх HTTP викликів або запитів до бази даних в юніт-тестах
8. **Використовуйте тестові бази даних** - Використовуйте транзакції з відкатом для інтеграційних тестів

## Запуск тестів

```bash
# Запустити всі тести
vendor/bin/phpunit

# Запустити конкретний набір
vendor/bin/phpunit --testsuite unit

# Запустити з покриттям
vendor/bin/phpunit --coverage-html coverage

# Запустити конкретний тест
vendor/bin/phpunit tests/Unit/UserServiceTest.php
```

## Наступні кроки

- [Налагодження](./debugging.md) - Налагодження додатків
- [Продуктивність](./performance.md) - Тестування продуктивності
- [CI/CD](./ci-cd.md) - Налаштування безперервної інтеграції
