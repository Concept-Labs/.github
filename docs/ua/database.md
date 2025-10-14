# Шар бази даних (DBC/DBAL)

Робота з базами даних за допомогою стека баз даних Concept-Labs.

## Огляд

Шар бази даних складається з двох компонентів:
- **DBC** (Database Connectivity) - З'єднання на основі PDO з передбачуваною поведінкою
- **DBAL** (Database Abstraction Layer) - Конструктори запитів та помічники, специфічні для діалекту

## DBC (Database Connectivity)

**DBC** надає з'єднання/драйвер/результат на основі PDO з передбачуваною поведінкою:
- Справжні підготовлені оператори (`ATTR_EMULATE_PREPARES=false` за замовчуванням)
- Асоціативний режим вибірки
- Простий API `query/prepare/exec/begin/commit/rollBack/lastInsertId`

## DBAL (Database Abstraction Layer)

**DBAL** надає:
- Адаптери діалектів (цитування ідентифікаторів, `LIMIT/OFFSET`, `RETURNING`, розв'язання конфліктів)
- SQL вирази, конструктори для `select/insert/update/delete`
- Управління зв'язуванням (`bind([...])`)
- Опційні помічники `returning()` / `onConflict()`

## Конфігурація

### Приклад PostgreSQL

`etc/db.default.json`:
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

## Плавне створення запитів

```php
$dbal = $container->get(Concept\DBAL\DmlManagerInterface::class);

// Insert з RETURNING (PostgreSQL)
$insert = $dbal->insert('users')
               ->values(['email' => ':email', 'name' => ':name'])
               ->bind(['email' => 'a@b.com', 'name' => 'Ann'])
               ->returning('id');

$dbc  = $container->get(Concept\DBC\ConnectionInterface::class);
$id   = (int)$dbc->query((string)$insert, $insert->getBindings())->fetchColumn();

// Select
$select = $dbal->select('users')->where('id = :id')->bind(['id' => $id]);
$user   = $dbc->query((string)$select, $select->getBindings())->fetch();

// Update
$update = $dbal->update('users')
               ->set(['name' => ':name'])
               ->where('id = :id')
               ->bind(['name' => 'Anna', 'id' => $id]);
$dbc->query((string)$update, $update->getBindings());

// Delete
$delete = $dbal->delete('users')->where('id = :id')->bind(['id' => $id]);
$dbc->query((string)$delete, $delete->getBindings());
```

## Транзакції

```php
$dbc->beginTransaction();
try {
    $dbc->query((string)$insert, $insert->getBindings());
    $dbc->query((string)$update, $update->getBindings());
    $dbc->commit();
} catch (\Throwable $e) {
    $dbc->rollBack();
    throw $e;
}
```

## Відмінності діалектів

### PostgreSQL

- Віддавайте перевагу `RETURNING` перед `lastInsertId()`
- Якщо викликаєте `lastInsertId`, передайте ім'я послідовності
- Ідентифікатори цитуються як `"schema"."table"`
- Масиви підтримуються через `= ANY(:ids)`

```php
$insert = $dbal->insert('users')
               ->values(['email' => ':email'])
               ->bind(['email' => 'user@example.com'])
               ->returning('id');
```

### MySQL/MariaDB

- Використовуйте `LAST_INSERT_ID()` або PDO `lastInsertId()` (немає `RETURNING` в MySQL < 8.0.27)
- Upsert через `INSERT ... ON DUPLICATE KEY UPDATE`
- Зворотні лапки для цитування ідентифікаторів (обробляється діалектом)

```php
$insert = $dbal->insert('users')
               ->values(['email' => ':email'])
               ->bind(['email' => 'user@example.com'])
               ->onConflict(['email'])
               ->doUpdate(['updated_at' => 'NOW()']);
```

## Типи зв'язування

Зв'яжіть скаляри з відповідними типами PDO (int/bool/null/string). Для масових масивів розгорніть до заповнювачів `:i1,:i2,...` або використовуйте помічники діалекту.

```php
// Одиночні значення
$select = $dbal->select('users')
               ->where('status = :status')
               ->bind(['status' => 'active'], [\PDO::PARAM_STR]);

// Значення масивів (PostgreSQL)
$select = $dbal->select('users')
               ->where('id = ANY(:ids)')
               ->bind(['ids' => [1, 2, 3]]);
```

## Найкращі практики

1. **Використовуйте підготовлені оператори** - Завжди зв'язуйте параметри, ніколи не конкатенуйте
2. **Використовуйте транзакції** - Обгортайте множинні операції в транзакції
3. **Усвідомлення діалекту** - Розумійте відмінності між PostgreSQL та MySQL
4. **Типобезпека** - Вказуйте типи параметрів PDO при зв'язуванні
5. **Обробка помилок** - Ловіть та обробляйте винятки бази даних відповідно

## Наступні кроки

- [Міграції](./migrations.md) - Міграції схеми бази даних
- [Тестування](./testing.md) - Тестування коду бази даних
- [Продуктивність](./performance.md) - Оптимізація бази даних

## Пов'язані ресурси

- [пакет dbal](https://github.com/Concept-Labs/dbal) - Шар абстракції бази даних
