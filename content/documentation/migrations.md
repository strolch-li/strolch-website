---
title: 'Migrations'
weight: 200
---

## Migrations

The Strolch Migration Framework allows for automated updates to the Strolch model and configuration across different versions of an application.

### Migration Component

The `PostgreSqlMigrationHandler` (or other implementation) manages the execution of migration scripts.

### Migration Scripts

Migrations are typically defined as XML files or SQL scripts located in a specific directory (e.g., `config/migrations/`).

Each migration has a version (e.g., `1.0.0`) and is executed only once per realm.

### Data Migrations

Data migrations allow for modifying the Strolch model (Resources, Orders, Activities) during an update.

### Configuration

Migrations can be configured to run automatically on agent startup:

```xml
<Component>
    <name>MigrationHandler</name>
    <api>li.strolch.handler.migration.MigrationHandler</api>
    <impl>li.strolch.persistence.postgresql.PostgreSqlMigrationHandler</impl>
    <Properties>
        <runMigrationsOnStart>true</runMigrationsOnStart>
        <verbose>true</verbose>
    </Properties>
</Component>
```
