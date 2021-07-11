---
title: 'Transactions' 
weight: 90
---

## Transactions

Strolch Transactions play a central role in a Strolch agent. A transaction is
opened for a realm, and grants access to the model of the agent. Transactions
are implemented as a Java `try-with-resources` by implementing
the `AutoCloseable`
interface. This makes it trivial to understand the scope of a transaction.

Transactions handle the following:

* Opening and closing database connections
* Releasing locks to strolch elements, if `tx.lock(StrolchRootElement)` or
  `tx.lock(Locator)` was called
* Performing Commands by executing them in the added order, and validating them
  first.
* Exception handling
* Auditing
* Updating observers

When a transaction is opened, it is by default read-only, i.e. does not perform
any commands when it is closed. Should the TX perform commands, then it is
important to call `tx.commitOnClose()`, but only at the end of the work, so that
exception handling can properly work if something goes wrong.

`StrolchTransaction` offers a myriad of methods:

* find element by its `Locator`
* get methods for elements by type and id, or using a `StringParameter` or
  `StringListParameter` references
* methods to add, update or remove elements
* assert privilege access
* get a new element by its template
* check if an element exists by type and id
* get streams for elements
* add commands for execution

Transactions are opened by accessing the realm, but there are convenience
methods depending on the use-case:

* In Services: by calling one of the `openTx()`-methods
* In Commands: Transactions are already open, use method `tx()` to get instance.
* REST API: `RestfulStrolchComponent.openTx()`

{{% notice warning %}}
Note: don't open a new TX inside a TX for the same realm!
{{% /notice %}}

Important is to always open the transaction as a `try-with-resource` block and to
define if the TX should commit, or not:
```java
try (StrolchTransaction tx = openTx(...)) {

  // read lock our object
  Locator ferrariLoc = Resource.locatorFor("Car", "ferrari");
  tx.lock(ferrariLoc);

  // find a car by locator
  Resource ferrari = tx.findElement(ferrariLoc);

  // get a car by ID
  Resource opel = tx.getResourceBy("Car", "opel", true);

  // modify ball
  opel.setName("Opel Corsa");
  tx.update(opel);

  // get by string reference
  StringParameter ownerP = ferrari.getParameter("relations", "owner", true);
  Resource owner = tx.getResourceBy(ownerP, true);

  // get by string list reference
  StringListParameter previousOwnersP = opel.getParameter("relations", "previousOwners", true);
  List<Resource> previousOwners = tx.getResourcesBy(previousOwnersP, true);

  // check resource exists
  if (tx.hasResource("Car", "audi")) {
    Resource audi = tx.getResourceBy("Car", "audi", true);

    // assert has privilege to remove a car
    tx.assertHasPrivilege(Operation.REMOVE, audi);

    // remove the car
    tx.remove(audi);
  }

  // iterate all cars
  tx.streamResources("Car").forEach(car -> {
  	logger.info("Car: " + car.getId());
  });

  // commit if TX was changed
  if (tx.needsCommit())
    tx.commitOnClose();
}
```
