# Посібник швидкого старту

Запустіться з Concept-Labs за лічені хвилини.

## Вимоги

- PHP 8.x
- Composer
- Веб-сервер (Nginx/Apache) або довготривале середовище виконання (RoadRunner/Swoole)

## Структура проекту

```
htdocs/
└─ pub/http.php        # точка входу
etc/
├─ default.json        # коренева конфігурація (імпорти)
├─ middleware.json     # HTTP конвеєр (пріоритети/до/після)
├─ router/route.json   # маршрути
└─ event-bus.json      # слухачі подій (необов'язково)
src/                   # ваш код
vendor/                # пакети (включаючи Concept‑Labs)
```

## Кроки встановлення

1. **Встановіть пакети через Composer** (Packagist/приватне репо/шлях)
   ```bash
   composer require concept-labs/simple-http
   composer require concept-labs/config
   composer require concept-labs/singularity
   ```

2. **Вкажіть веб-корінь на `pub/`**
   Налаштуйте ваш веб-сервер для обслуговування з директорії `pub/`

3. **Додайте/імпортуйте конфігурації в `etc/`**
   Створіть ваші файли конфігурації, дотримуючись прикладів нижче

4. **Запустіть ваш сервер** (або RoadRunner worker) і відвідайте `/`

## Приклад конфігурації

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

## Наступні кроки

- [Посібник з конфігурації](./configuration.md) - Дізнайтеся про DSL конфігурації
- [Впровадження залежностей](./dependency-injection.md) - Зрозумійте Singularity DI
- [HTTP стек](./http-stack.md) - Побудуйте ваш HTTP додаток
- [Огляд архітектури](./architecture.md) - Глибоке занурення в дизайн системи
