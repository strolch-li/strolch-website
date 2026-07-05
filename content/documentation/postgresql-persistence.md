---
title: 'PostgreSQL Persistence'
weight: 210
---

## PostgreSQL Persistence

The `strolch-persistence-postgresql` module provides a PostgreSQL-based persistence implementation for the Strolch framework.

### Features

-   Stores Strolch elements (Resources, Orders, Activities, Audits, and LogMessages) in a PostgreSQL database.
-   Support for multi-tenancy (multiple realms).
-   Automated schema management and migrations.

### Configuration

To use PostgreSQL persistence, configure the `PersistenceHandler` in `StrolchConfiguration.xml`.

```xml
<Component>
    <name>PersistenceHandler</name>
    <api>li.strolch.persistence.api.PersistenceHandler</api>
    <impl>li.strolch.persistence.postgresql.PostgreSqlPersistenceHandler</impl>
    <Properties>
        <db.url>jdbc:postgresql://localhost/strolchdb</db.url>
        <db.username>strolch</db.username>
        <db.password>password</db.password>
    </Properties>
</Component>
```

For multi-realm configurations, properties can be specified per realm:

```xml
<db.url.myrealm>jdbc:postgresql://localhost/myrealmdb</db.url.myrealm>
```

### Schema Management

The `PostgreSqlPersistenceHandler` can automatically create and update the database schema.

-   `allowSchemaCreation`: (Boolean) If true, the schema is created if it does not exist.
-   `allowSchemaDrop`: (Boolean) If true, allows dropping the schema (use with caution!).
