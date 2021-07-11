---
title: 'Versioning'
weight: 120
---

## Versioning

One of Strolch's features that sets it apart from other frameworks, is that
versioning is baked into Strolch's fabric. The feature is opt-in, as it is not
required in all projects, but it only needs enabling, for all modifications to
objects to be versioned, so that rollbacks can be done when needed.

The feature is enabled for each realm. In the `StrolchConfiguration.xml` file
enable it by adding the `enableVersioning` propery per realm:

```xml
<StrolchConfiguration>
  <env id="dev">
    ...
    <Component>
      <name>RealmHandler</name>
      <api>li.strolch.agent.api.RealmHandler</api>
      <impl>li.strolch.agent.impl.DefaultRealmHandler</impl>
      <depends>PrivilegeHandler</depends>
      <Properties>
        <realms>defaultRealm, otherRealm</realms>
        <enableVersioning>true</enableVersioning>
        <dataStoreMode>TRANSIENT</dataStoreMode>
        <dataStoreFile>StrolchModel.xml</dataStoreFile>
        <enableVersioning.otherRealm>true</enableVersioning.otherRealm>
        <dataStoreMode.otherRealm>TRANSIENT</dataStoreMode.otherRealm>
        <dataStoreFile.otherRealm>StrolchModel.xml</dataStoreFile.otherRealm>
      </Properties>
    </Component>
  </env>
  ...
</StrolchConfiguration>
```

Once versioning is enabled, versioning is handled automatically. The API for versioning is implemented on the ElementMaps.

Example: Revert to previous version of a Resource:

```java
Resource res = tx.getResourceBy("TestType", "MyTestResource");
ResourceMap resourceMap = tx.getResourceMap();
Resource previousVersion = resourceMap.revertToVersion(tx, res);
// or
Resource previousVersion = resourceMap.revertToVersion(tx, "TestType", "MyTestResource", 1);
```

Example: Retrieve all versions of a Resource:

```java
List<Resource> versions = resourceMap.getVersionsFor(tx, "TestType", "MyTestResource");
```

{{% notice tip %}}
Note: When reverting to a previous version, it is important to remember, that
any references on an element to other elements will also be restored. As long as
the relationship is to the same element, then this is not an issue, but should
the relationship have changed, then it this must be handled and the user
performing a revert be allowed to decided which element to reference in the
reverted version.
{{% /notice %}}
