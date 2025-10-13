# Database Layer (DBC/DBAL)

Working with databases using the Concept-Labs database stack.

## Overview

The database layer consists of two components:
- **DBC** (Database Connectivity) - PDO-backed connections with predictable behavior
- **DBAL** (Database Abstraction Layer) - Dialect-specific query builders and helpers

## DBC (Database Connectivity)

**DBC** provides PDO-backed connection/driver/result with predictable behavior:
- Real prepared statements (`ATTR_EMULATE_PREPARES=false` by default)
- Associative fetch mode
- Simple `query/prepare/exec/begin/commit/rollBack/lastInsertId` API

## DBAL (Database Abstraction Layer)

**DBAL** provides:
- Dialect adapters (quoting identifiers, `LIMIT/OFFSET`, `RETURNING`, conflict resolution)
- SQL expressions, builders for `select/insert/update/delete`
- Binding management (`bind([...])`)
- Optional `returning()` / `onConflict()` helpers

## Configuration

### PostgreSQL Example

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

## Fluent Query Building

```php
$dbal = $container->get(Concept\DBAL\DmlManagerInterface::class);

// Insert with RETURNING (PostgreSQL)
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

## Transactions

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

## Dialect Differences

### PostgreSQL

- Prefer `RETURNING` over `lastInsertId()`
- If you call `lastInsertId`, pass the sequence name
- Identifiers quoted as `"schema"."table"`
- Arrays supported via `= ANY(:ids)`

```php
$insert = $dbal->insert('users')
               ->values(['email' => ':email'])
               ->bind(['email' => 'user@example.com'])
               ->returning('id');
```

### MySQL/MariaDB

- Use `LAST_INSERT_ID()` or PDO `lastInsertId()` (no `RETURNING` in MySQL < 8.0.27)
- Upsert via `INSERT ... ON DUPLICATE KEY UPDATE`
- Backticks for quoting identifiers (handled by the dialect)

```php
$insert = $dbal->insert('users')
               ->values(['email' => ':email'])
               ->bind(['email' => 'user@example.com'])
               ->onConflict(['email'])
               ->doUpdate(['updated_at' => 'NOW()']);
```

## Binding Types

Bind scalars with appropriate PDO types (int/bool/null/string). For bulk arrays, expand to `:i1,:i2,...` placeholders or use dialect helpers.

```php
// Single values
$select = $dbal->select('users')
               ->where('status = :status')
               ->bind(['status' => 'active'], [\PDO::PARAM_STR]);

// Array values (PostgreSQL)
$select = $dbal->select('users')
               ->where('id = ANY(:ids)')
               ->bind(['ids' => [1, 2, 3]]);
```

## Best Practices

1. **Use prepared statements** - Always bind parameters, never concatenate
2. **Use transactions** - Wrap multiple operations in transactions
3. **Dialect awareness** - Understand differences between PostgreSQL and MySQL
4. **Type safety** - Specify PDO parameter types when binding
5. **Error handling** - Catch and handle database exceptions appropriately

## Next Steps

- [Migrations](./migrations.md) - Database schema migrations
- [Testing](./testing.md) - Testing database code
- [Performance](./performance.md) - Database optimization

## Related Resources

- [dbal package](https://github.com/Concept-Labs/dbal) - Database abstraction layer
