---
title: 'Realms'
weight: 50
---

## Realms

Realms implement multi-tenant capabilities. A Strolch agent can have an arbitrary number of realms configured and each
realm has its own data maps (Resources, Orders, Activities) and can be configured independently.

### RealmHandler

The `RealmHandler` is a `StrolchComponent` that manages the lifecycle of all configured realms. It provides access to
the realms by name.

A realm can run in one of the following modes:

* **EMPTY**: This is a transient data store mode, where no model changes are persisted - they are only kept in memory.
  When the Strolch agent is started, this realm is empty as no data is loaded.
* **TRANSIENT**: This is the same as EMPTY, but with the difference that when the Strolch agent is started, a model file
  is parsed and the in-memory realm is populated with the elements parsed from the model file.
* **CACHED**: In this mode, all data is stored in-memory, and any changes made are written back to the persistence
  layer. This allows for fast in-memory queries, but makes sure no data is lost when the agent is restarted.

Realms are mostly hidden from a developer as a `StrolchTransaction` exposes all important operations needed to access
Strolch objects. A developer will however need to configure the realms for their specific project.

### Configuration

The configuration in the `StrolchConfiguration.xml` file is as follows:

```xml

<Component>
    <name>RealmHandler</name>
    <api>li.strolch.agent.api.RealmHandler</api>
    <impl>li.strolch.agent.impl.DefaultRealmHandler</impl>
    <depends>PrivilegeHandler</depends>
    <Properties>
        <!-- one of EMPTY, TRANSIENT, CACHED-->
        <dataStoreMode>TRANSIENT</dataStoreMode>
        <dataStoreFile>StrolchModel.xml</dataStoreFile>
    </Properties>
</Component>
```

#### Multi-Realm Configuration

A multi-realm configuration allows defining different settings for each realm:

```xml

<Component>
    <name>RealmHandler</name>
    <api>li.strolch.agent.api.RealmHandler</api>
    <impl>li.strolch.agent.impl.DefaultRealmHandler</impl>
    <Properties>
        <realms>defaultRealm, cachedRealm</realms>
        <dataStoreMode>TRANSIENT</dataStoreMode>
        <dataStoreFile>DefaultRealm.xml</dataStoreFile>
        <dataStoreMode.cachedRealm>CACHED</dataStoreMode.cachedRealm>
        <dataStoreMode.emptyRealm>EMPTY</dataStoreMode.emptyRealm>
    </Properties>
</Component>
```

### Persistence

If a realm uses the `CACHED` mode, the `PersistenceHandler` must be configured. Each realm can have its own database
connection:

```xml

<Component>
    <name>PersistenceHandler</name>
    <api>li.strolch.persistence.api.PersistenceHandler</api>
    <impl>li.strolch.persistence.postgresql.PostgreSqlPersistenceHandler</impl>
    <Properties>
        <db.url.cachedRealm>jdbc:postgresql://localhost/testdb</db.url.cachedRealm>
        <db.username.cachedRealm>user</db.username.cachedRealm>
        <db.password.cachedRealm>password</db.password.cachedRealm>
    </Properties>
</Component>
```

### Accessing a Realm

Accessing a realm is done via the `ComponentContainer`. However, it is recommended to interact with the model via
transactions.

```java
StrolchRealm realm = container.getRealm(StrolchConstants.DEFAULT_REALM);
try(
StrolchTransaction tx = realm.openTx(certificate, "Action", false)){
		// ...
		}
```
