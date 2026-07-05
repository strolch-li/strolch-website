---
title: 'XML Persistence'
weight: 220
---

## XML Persistence

The `strolch-persistence-xml` module provides a file-based XML persistence implementation. It is ideal for small projects, development, or when a full database is not required.

### Features

-   Stores Strolch elements as XML files on the file system.
-   Elements are organized in directories by type.
-   Fast startup by reading all elements into memory (CACHED mode).

### Configuration

To use XML persistence, configure the `PersistenceHandler` in `StrolchConfiguration.xml`.

```xml
<Component>
    <name>PersistenceHandler</name>
    <api>li.strolch.persistence.api.PersistenceHandler</api>
    <impl>li.strolch.persistence.xml.XmlPersistenceHandler</impl>
    <Properties>
        <dbStorePath>data/db</dbStorePath>
    </Properties>
</Component>
```

The `dbStorePath` property defines the root directory where the XML files are stored. For multi-realm configurations, use:

```xml
<dbStorePath.myrealm>data/db_myrealm</dbStorePath.myrealm>
```

### Implementation Details

-   **Root Elements**: Each Resource, Order, and Activity is stored in its own XML file.
-   **Audits and LogMessages**: Also stored as XML files.
-   **Performance**: Since all elements are typically loaded into memory in `CACHED` mode, query performance is very high. However, write operations require serializing the entire element to disk.
