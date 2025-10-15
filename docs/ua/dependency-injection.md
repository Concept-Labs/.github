# Впровадження залежностей (Singularity)

Повний посібник по контейнеру DI Singularity.

## Огляд

Кожен пакет постачається з `concept.json` зі своїми перевагами. Додаток може додавати/перевизначати власні. Singularity — це PSR-11 сумісний контейнер впровадження залежностей, розроблений для ясності та гнучкості.

## Конфігурація

### На рівні пакета (всередині пакета vendor)

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

### Перевизначення на рівні додатка (ваше репо)

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

## Ключові концепції

### Переваги інтерфейсів
Прив'яжіть інтерфейси до конкретних реалізацій:

```php
// Контейнер розв'язує інтерфейси на основі переваг
$app = $container->get(AppInterface::class);
// Повертає екземпляр Concept\Http\App або App\Http\CustomApp
```

### Патерни життєвого циклу

- **Transient** - Новий екземпляр кожен раз
- **Shared** (Singleton) - Один екземпляр на весь додаток
- **Weak** - Спільний, але може бути зібраний збирачем сміття
- **Prototype** - Клонований з екземпляра-прототипу

### Autowiring

Контейнер може автоматично розв'язувати залежності:

```php
class UserController {
    public function __construct(
        private UserRepository $users,
        private Logger $logger
    ) {}
}

// Контейнер автоматично розв'язує залежності
$controller = $container->get(UserController::class);
```

## Найкращі практики

### Детермінізм
Якщо два пакети зв'язують один і той же інтерфейс, власні переваги додатка повинні перемогти (політика в розробці/ альтернативно використовується сортування файлової системи).

### Фабрики
Віддавайте перевагу невеликим фабрикам перед важкими конструкторами; з'єднуйте фабрики через DI для слухачів, обробників та middleware.

```php
class ControllerFactory {
    public function create(): ControllerInterface {
        return new Controller(/* залежності */);
    }
}
```

### Scoping
У довготривалих воркерах уникайте захоплення контейнера в замиканнях; розв'язуйте на запит або передавайте невеликі об'єкти-значення.

### Виявляємість
Тримайте переваги близько до пакета, до якого вони належать—це покращує самодокументацію.

## Розширені можливості

### Впровадження методів
Сервіси можуть реалізовувати `InjectableInterface` для автоматичного впровадження методів:

```php
class Service implements InjectableInterface {
    public function inject(LoggerInterface $logger): void {
        $this->logger = $logger;
    }
}
```

### Ледаче завантаження
Використовуйте `LazyGhostInterface` для сервісів, які повинні створюватися лише при використанні:

```php
class ExpensiveService implements LazyGhostInterface {
    // Створюється лише при виклику методів
}
```

### Система плагінів
Розширюйте поведінку контейнера за допомогою плагінів:

```php
$container->registerPlugin(new CustomPlugin());
```

## Тестування

Контейнер спрощує тестування:

```php
// Перевизначте сервіси для тестування
$container->set(ApiClientInterface::class, new MockApiClient());

// Тестуйте з реальними залежностями
$service = $container->get(ServiceToTest::class);
```

## Наступні кроки

- [Конфігурація](./configuration.md) - Налаштуйте контейнер
- [Розширені патерни](./advanced-patterns.md) - Складні сценарії DI
- [Посібник з тестування](./testing.md) - Тестування з DI

## Пов'язані ресурси

- [пакет singularity](https://github.com/Concept-Labs/singularity) - Повна документація DI
