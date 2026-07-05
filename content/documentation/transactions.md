---
title: 'Transactions'
weight: 90
---

## Transactions

Strolch Transactions play a central role in a Strolch agent. A transaction is opened for a realm, and grants access to the model of the agent. Transactions are implemented as a Java `try-with-resources` by implementing the `AutoCloseable` interface. This makes it trivial to understand the scope of a transaction.

Transactions handle the following:

*   Opening and closing database connections
*   Releasing locks to strolch elements
*   Performing Commands by executing them in the added order, and validating them first
*   Exception handling
*   Auditing
*   Updating observers

### Opening a Transaction

Transactions are opened via the `StrolchAgent` or a `StrolchComponent`.

```java
try (StrolchTransaction tx = agent.openTx(certificate, "MyAction", readOnly)) {
    // Perform operations
}
```

*   **`certificate`**: Identifies the user and their privileges.
*   **`action`**: A string naming the action, used for auditing and logging.
*   **`readOnly`**: A boolean flag.
    *   If `true`, the transaction is strictly read-only. Any attempt to modify the model or add commands will throw an exception.
    *   If `false`, the transaction is writeable.

Transactions are opened by accessing the realm, but there are convenience methods depending on the use-case:

*   In **Services**: by calling one of the `openTx()`-methods.
*   In **Commands**: Transactions are already open, use method `tx()` to get instance.
*   **REST API**: `RestfulStrolchComponent.openTx()`.

{{% notice warning %}}
Note: don't open a new TX inside a TX for the same realm!
{{% /notice %}}

### Transaction Outcome and Best Practices

For writeable transactions where changes are made, you **must** explicitly define the outcome. If a transaction is closed with uncommitted changes, an exception will be thrown.

The recommended pattern for writeable transactions is:

```java
try (StrolchTransaction tx = agent.openTx(certificate, "UpdateResource", false).rollbackOnFailure()) {
    // ... perform operations ...
    tx.commitOnClose();
}
```

*   `rollbackOnFailure()`: Configures the transaction to automatically roll back if an exception occurs. This avoids the "modified elements which will not be committed" exception that can mask the original error.
*   `commitOnClose()`: Must be called at the end of the block to ensure all changes (added/updated/removed elements and commands) are persisted when the transaction closes successfully.
*   `rollbackOnClose()`: Can be used to explicitly roll back all changes upon closing.

### Interacting with the Model

While `StrolchTransaction` provides access to `ResourceMap`, `OrderMap`, and `ActivityMap`, **these should never be used directly**. Instead, use the convenience methods provided by the `StrolchTransaction` class.

#### Retrieving and Finding Elements
*   `getResourceBy(type, id, assertExists)` / `getOrderBy(...)` / `getActivityBy(...)`: Retrieves a root element.
*   `getResourceBy(StringParameter refP, assertExists)`: Retrieves an element referenced by a parameter.
*   `findElement(locator)`: Finds any element (Resource, Order, Activity, Bag, Parameter, etc.) by its locator.
*   `findParameterOnHierarchy(element, parentParamKey, bagKey, paramKey)`: Searches for a parameter up a defined hierarchy (e.g., following relations).

#### Modifying Elements
*   `add(element)`: Adds a new root element.
*   `update(element)`: Updates an existing root element.
*   `remove(element)`: Removes a root element.
*   `addOrUpdate(element)`: Adds the element if it doesn't exist, otherwise updates it.

### Locking

Strolch uses a locking mechanism to ensure thread safety. **Elements are never locked automatically upon retrieval.** You must explicitly lock elements you intend to modify.

*   `tx.lock(element)` or `tx.lock(locator)`: Acquires a lock on the specified element.
*   `tx.readLock(element)`: **Recommended when modifying.** This method acquires a lock and then retrieves a *fresh copy* of the element from the database, ensuring you are working with the latest data under lock.

### Commands

Complex or reusable business logic should be encapsulated in `Command` objects and added to the transaction. Commands are validated and executed when the transaction is flushed or committed.

The recommended pattern is to instantiate the command, configure it, and then add it to the transaction:

```java
try (StrolchTransaction tx = openTx(certificate, "MyAction", false).rollbackOnFailure()) {
    MyCommand command = new MyCommand(tx);
    command.setArg1(value1);
    
    // add to TX for execution on commit
    tx.addCommand(command);

    tx.commitOnClose();
}
```

Two lifecycle methods are available for commands:
*   `command.validate()`: Called automatically before execution to verify preconditions.
*   `command.doCommand()`: Called automatically to perform the business logic.

### Auditing and Privileges

*   **Auditing**: All write operations are automatically audited if enabled. Auditing can be suppressed for specific transactions using `tx.suppressAudits()`.
*   **Privilege Assertions**: Use the transaction to verify user permissions:
    *   `tx.assertHasPrivilege(Operation.UPDATE, resource)`: Throws `AccessDeniedException` if the user lacks the privilege.

### Full Example

```java
try (StrolchTransaction tx = openTx(certificate, "ModifyCar", false).rollbackOnFailure()) {

  // get a car by ID and lock it
  Resource opel = tx.getResourceBy("Car", "opel", true);
  tx.lock(opel);

  // modify car
  opel.setName("Opel Corsa");
  tx.update(opel);
  
  // Alternative: use readLock to get a fresh copy and lock in one go
  Resource ferrari = tx.readLock(Resource.locatorFor("Car", "ferrari"));
  ferrari.setName("Ferrari F40");
  tx.update(ferrari);

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

  // commit the changes
  tx.commitOnClose();
}
```
