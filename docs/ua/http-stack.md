# HTTP стек та конвеєр Middleware

Створення веб-додатків з HTTP стеком Concept-Labs.

## Огляд

HTTP стек побудований на стандартах PSR-7, PSR-15 та PSR-17, надаючи потужний та гнучкий конвеєр обробки запитів на основі middleware.

## Конфігурація Middleware

Оголосіть middleware в `etc/middleware.json`. Вищий пріоритет виконується раніше.

### Смуги пріоритету

Зарезервуйте "смуги" для підтримки стабільної ментальної моделі (рекомендовано, але не обов'язково):

- **9000–8000: boot** (прапорці функцій, ініціалізація event bus)
- **7900–7000: security** (CORS, автентифікація, CSRF, обмеження швидкості)
- **6900–6000: infra** (сесія, локаль, парсери тіла)
- **5900–5000: routing**
- **4900–4000: handler adapters**
- **3900–3000: response transformers** (шаблонізація, нормалізатори)
- **1100–1000: telemetry** (логування/метрики)
- **≤ 100: terminal** (404, межа throwable, flusher)

### Приклад конфігурації

```json
{
  "concept-labs/event-dispatcher:event-bus": {
    "preference": "Concept\\EventDispatcher\\Middleware\\EventBusMiddleware",
    "priority": 9000
  },
  "concept-labs/http:router": {
    "preference": "Concept\\Http\\Router\\RouterMiddleware",
    "priority": 5800
  },
  "concept-labs/http:flusher": {
    "preference": "Concept\\Http\\Middleware\\ResponseFlusherMiddleware",
    "priority": 100
  }
}
```

## Потік запиту

```
Запит → [Безпека] → [Сесія] → [Маршрутизатор] → [Обробник] → [Views] → Відповідь
```

Кожен middleware може:
1. Обробити запит перед передачею далі
2. Передати запит наступному middleware
3. Обробити відповідь перед поверненням
4. Перервати конвеєр (наприклад, при невдалій автентифікації)

## Створення Middleware

Створіть власний middleware, реалізувавши PSR-15 `MiddlewareInterface`:

```php
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Server\MiddlewareInterface;
use Psr\Http\Server\RequestHandlerInterface;

class CustomMiddleware implements MiddlewareInterface
{
    public function process(
        ServerRequestInterface $request,
        RequestHandlerInterface $handler
    ): ResponseInterface {
        // До: модифікуйте запит або додайте атрибути
        $request = $request->withAttribute('custom', 'value');
        
        // Викликайте наступний middleware
        $response = $handler->handle($request);
        
        // Після: модифікуйте відповідь
        return $response->withHeader('X-Custom', 'Header');
    }
}
```

## Маршрутизація

Налаштуйте маршрути в `etc/router/route.json`:

```json
{
  "routes": {
    "home": {
      "path": "/",
      "methods": ["GET"],
      "handler": "App\\Handler\\HomeHandler"
    },
    "user.profile": {
      "path": "/users/{id}",
      "methods": ["GET"],
      "handler": "App\\Handler\\UserProfileHandler"
    }
  }
}
```

## Обробка помилок

Централізуйте помилки в одному boundary middleware:

- Перетворює винятки в HTML або JSON (узгодження вмісту)
- Випускає консистентні дані (розгляньте RFC 7807 Problem Details)
- Логує через PSR-3 з ID кореляції запитів

Тримайте **404/405** та **flusher** як термінальний middleware з дуже низьким пріоритетом (виконується останнім).

## Безпека

### CORS та CSRF
Надайте виділений middleware в смузі безпеки (7xxx).

### Автентифікація
Перервіть рано при неавтентифікованому доступі; тримайте обробники не в курсі деталей транспорту.

### Заголовки
Встановіть заголовки безпеки:
- `X-Frame-Options`
- `X-Content-Type-Options`
- `Referrer-Policy`
- `Strict-Transport-Security`

### Сесії
Регенеруйте ID при зміні привілеїв; встановіть `SameSite`, `HttpOnly`, `Secure` відповідно.

## Продуктивність

### Довготривалі середовища виконання

Для RoadRunner/Swoole:
- Компілюйте конвеєри один раз (опційне топо-сортування) та кешуйте з `revisionId`
- Уникайте захоплення `$this` у замиканнях часу життя запиту
- Переконайтеся, що агрегація подій та завантаження контейнера ідемпотентні
- Вимірюйте затримку кожного middleware (µs)

### OPcache
Увімкніть та прогрійте; тримайте файли незмінними між релізами для максимальних попадань кешу.

## Наступні кроки

- [Посібник з маршрутизації](./routing.md) - Розширені патерни маршрутизації
- [Обробка помилок](./error-handling.md) - Комплексне управління помилками
- [Посібник з безпеки](./security.md) - Найкращі практики безпеки
- [Продуктивність](./performance.md) - Техніки оптимізації

## Пов'язані ресурси

- [пакет http](https://github.com/Concept-Labs/http) - Основна HTTP функціональність
- [пакет simple-http](https://github.com/Concept-Labs/simple-http) - Швидкі HTTP додатки
- [пакет http-message](https://github.com/Concept-Labs/http-message) - Реалізації PSR-7
