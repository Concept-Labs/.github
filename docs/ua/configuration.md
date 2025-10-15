# DSL конфігурації

Повний посібник по системі конфігурації Concept-Labs.

## Огляд

Конфігурація — це JSON з невеликим DSL, який розв'язується при завантаженні. Система надає потужний, але простий спосіб керування налаштуваннями додатка в різних середовищах.

## Директиви та вирази

### @import
Включайте та об'єднуйте інші JSON файли за порядком (останній перемагає при конфліктах).

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
Читайте змінні оточення з резервним значенням за замовчуванням.

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
Вбудована підстановка з поточного дерева конфігурації.

```json
{
  "paths": {
    "root": "/var/www",
    "public": "${paths.root}/public",
    "storage": "${paths.root}/storage"
  }
}
```

## Порядок розв'язання

Розуміння того, як обробляється конфігурація:

1. **Завантажити корінь** (наприклад, `etc/default.json`)
2. **Розгорнути @import** за порядком; об'єднати неглибокі мапи за ключем, додати масиви, якщо явно не задокументовано інакше в даному вузлі
3. **Обчислити директиви @env**
4. **Застосувати підстановки ${...}** після того, як всі імпорти/env присутні
5. **Обробити конфігурації пакетів** - Деякі пакети можуть оголосити `extra.concept.include` (або подібне) в `composer.json`, щоб автоматично підхоплюватися завантажувачем додатка (якщо ви його увімкнете)

## Розширені можливості

### Екранування підстановок
Напишіть `\${...}`, щоб отримати літеральне `${...}`.

### Крапковий доступ
Через DotArray/DotQuery (наприклад, `config->node('dbc.connection.dsn')`).

```php
$config->get('database.host');
$config->set('cache.driver', 'redis');
$config->has('app.debug');
```

## Конфігурація пакета

Кожен пакет може надати свій власний `concept.json` для самостійної конфігурації:

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

## Приклади конфігурацій

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

## Найкращі практики

1. **Тримайте секрети поза VCS** - Використовуйте змінні оточення для конфіденційних даних
2. **Надавайте .dist шаблони** - Включайте приклади конфігурацій
3. **Використовуйте конфігурацію на рівні пакета** - Тримайте конфігурацію близько до коду, який її використовує
4. **Документуйте власні директиви** - Якщо ви додаєте власні функції DSL, документуйте їх
5. **Контролюйте версії конфігурацій** - Відстежуйте зміни конфігурації разом з кодом

## Наступні кроки

- [Впровадження залежностей](./dependency-injection.md) - Налаштуйте контейнер DI
- [HTTP стек](./http-stack.md) - Налаштуйте middleware та маршрутизацію
- [Система подій](./events.md) - Налаштуйте слухачів подій
- [Шар бази даних](./database.md) - Налаштуйте з'єднання з базою даних

## Пов'язані ресурси

- [пакет concept-labs/config](https://github.com/Concept-Labs/config) - Повна документація з конфігурації
