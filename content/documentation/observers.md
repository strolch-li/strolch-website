---
title: 'Observers'
weight: 110
---

## Observers

All changes done in a Strolch transaction are recorded and then propagated to
any registered observers.

The observer feature is opt-in and is configured for each realm. In the
`StrolchConfiguration.xml` file enable observers by adding the
`enableObserverUpdates` property per realm:

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
        <enableObserverUpdates>true</enableObserverUpdates>
        <dataStoreMode>TRANSIENT</dataStoreMode>
        <dataStoreFile>StrolchModel.xml</dataStoreFile>
        <enableObserverUpdates.otherRealm>true</enableObserverUpdates.otherRealm>
        <dataStoreMode.otherRealm>TRANSIENT</dataStoreMode.otherRealm>
        <dataStoreFile.otherRealm>StrolchModel.xml</dataStoreFile.otherRealm>
      </Properties>
    </Component>
  </env>
  ...
</StrolchConfiguration>
```

Registering for updates is done by registering an Observer on the
ObserverHandler of the realm itself:

```java
public class Example {
	public static void main(String[] args) {

		ObserverHandler observerHandler = container
				.getRealm(StrolchConstants.DEFAULT_REALM).getObserverHandler();
		observerHandler.registerObserver(Tags.RESOURCE, new Observer() {

			@Override
			public void update(String key, List<StrolchRootElement> elements) {
				logger.info(elements.size() + " resources were updated!");
			}

			@Override
			public void remove(String key, List<StrolchRootElement> elements) {
				logger.info(elements.size() + " resources were removed!");
			}

			@Override
			public void add(String key, List<StrolchRootElement> elements) {
				logger.info(elements.size() + " resources were added!");
			}
		});
	}
}
```

