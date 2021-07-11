---
title: 'Realms'
weight: 50
---

## Realms
Realms implement multi-tenant capabilities. A Strolch agent can have an 
arbitrary number of realms configured and each realm has its own persistence 
configuration, allowing to separate mandates completely.

A realm can run in one of the following modes:
* EMPTY 
  This is a transient data store mode, where no model changes are 
  persisted - they are only kept in memory. When the Strolch agent is 
  started, this realm is empty as no data is loaded.
* TRANSIENT
  This is the same as EMPTY, but with the difference that when the Strolch 
  agent is started, a model file is parsed and the in-memory realm is 
  populated with the elements parsed from the model file.
* CACHED
  In this mode, all data is stored in-memory, and any changes made are 
  written back to the persistence layer. This allows for fast in-memory 
  qeuries, but makes sure no data is lost when the agent is restarted.

Realms are mostly hidden from a developer as a `StrolchTransaction` exposes 
all important operations needed to access Strolch objects. A developer will 
however need to configure the realms for their specific project. If the 
project only requires one realm, then the `defaultRealm` can be used, where the 
developer only is required to configure the mode and any relevant model file.

If the mode is `CACHED`, then the `PersistenceHandler` component is required to be 
configured, so that the DAOs know how to access the underlying database.

The configuration in the `StrolchConfiguration.xml` file is as follows:

```xml
<StrolchConfiguration>
  <env id="dev">
    ...
    <Component>
      <name>RealmHandler</name>
      <api>li.strolch.agent.api.RealmHandler</api>
      <impl>li.strolch.agent.impl.DefaultRealmHandler</impl>
      <depends>PrivilegeHandler</depends>
      <!-- if CACHED: -->
      <!--depends>PersistenceHandler</depends-->
      <Properties>
        <dataStoreMode>EMPTY|TRANSIENT|CACHED</dataStoreMode>
        <dataStoreFile>StrolchModel.xml</dataStoreFile>
      </Properties>
    </Component>
    ...
  </env>
</StrolchConfiguration>
```

## Multi-Realm
A multi-realm configuration would be as follows. 

{{% notice tip %}}
Note how the defaultRealm is still enabled, and has its configuration as
before. Further the PostgreSQL PersistenceHandler is configured to show how the
realms are connected to the persistence handler:
{{% /notice %}}

```xml
<StrolchConfiguration>
  <env id="dev">
    ...
    <Component>
      <name>RealmHandler</name>
      <api>li.strolch.agent.api.RealmHandler</api>
      <impl>li.strolch.agent.impl.DefaultRealmHandler</impl>
      <depends>PrivilegeHandler</depends>
      <depends>PersistenceHandler</depends>
      <Properties>
        <realms>defaultRealm, cachedRealm</realms>
        <dataStoreMode>TRANSIENT</dataStoreMode>
        <dataStoreFile>DefaultRealm.xml</dataStoreFile>
        <dataStoreMode.cachedRealm>CACHED</dataStoreMode.cachedRealm>
        <dataStoreMode.emptyRealm>EMPTY</dataStoreMode.emptyRealm>
      </Properties>
    </Component>

    <Component>
      <name>PersistenceHandler</name>
      <api>li.strolch.persistence.api.PersistenceHandler</api>
      <impl>li.strolch.persistence.postgresql.PostgreSqlPersistenceHandler</impl>
      <Properties>
        <allowSchemaCreation>true</allowSchemaCreation>
        <allowSchemaDrop>true</allowSchemaDrop>

        <db.url.cachedRealm>jdbc:postgresql://localhost/testdb2</db.url.cachedRealm>
        <db.username.cachedRealm>testuser2</db.username.cachedRealm>
        <db.password.cachedRealm>test</db.password.cachedRealm>
        <db.pool.maximumPoolSize.cachedRealm>1</db.pool.maximumPoolSize.cachedRealm>
      </Properties>
    </Component>
    ...
  </env>
</StrolchConfiguration>
```

## Access realm
Accessing a realm is done in multiple ways. Important is to note, that a user 
should use the `StrolchTransaction` object, instead of accessing the Realm directly.

Opening a transaction is done from a `Service` by calling one of the 
`openTx()`-methods. Nevertheless, the realm can be accessed as follows:

```java
ComponentContainer container = getAgent().getContainer();
StrolchRealm realm = container.getRealm(StrolchConstants.DEFAULT_REALM);
try(StrolchTransaction tx = realm.openTx()) {
  Resource resource = tx.getResourceBy("TestType", "MyTestResource");
  ...
}
```
