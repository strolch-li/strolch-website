---
title: 'Services and Commands'
weight: 70
---

## Services and Commands

`Services` are the primary entry points for business logic in Strolch. They are designed to be called from REST resources, UIs, or other high-level components. `Commands` are atomic, reusable operations that are performed within a transaction.

### Services

Services extend the abstract class `AbstractService<T, U>` and then implement the method `internalDoService(T arg)`. `T` is the argument type (extending `ServiceArgument`) and `U` is the result type (extending `ServiceResult`).

Key responsibilities of `AbstractService`:
*   **Transaction management**: It facilitates opening and closing transactions.
*   **Privilege checking**: It ensures the user has the necessary permissions.
*   **Result handling**: It ensures a consistent result object is returned.

The `AbstractService` class has multiple helper methods:
*   `openTx(String realm)`: Opens a transaction for the given realm.
*   `openArgOrUserTx(ServiceArgument arg)`: Opens a transaction based on the argument.
*   `runPrivileged(PrivilegedRunnable runnable)`: Performs a task with system privileges.
*   `getComponent(Class<V> clazz)`: Retrieves a specific `StrolchComponent`.

#### Service Result

Services return a `ServiceResult` (or a subclass). It indicates whether the operation was successful and carries any result data or error messages.

*   `ServiceResult.success()`: Returns a successful result.
*   `ServiceResult.error(String msg)`: Returns an error result with a message.

### Commands

Commands extend the `Command` class and implement the method `doCommand()`. They are used within a transaction to perform a specific task.

Key methods to implement:
*   `validate()`: Perform pre-condition checks.
*   `doCommand()`: The actual logic.
*   `undo()`: Optional. Logic to revert changes if the transaction is rolled back.

Commands have helper methods:
*   `tx()`: To get the current transaction.
*   `getPolicy(Class<V> clazz, PolicyDef policyDef)`: To retrieve a `StrolchPolicy` instance.

### Example: Adding an Order

The following code snippets shows how a Service and Command are used to perform the task of adding a new Order.

**AddOrderService**:

```java
public class AddOrderService extends AbstractService<AddOrderService.AddOrderArg, ServiceResult> {

  @Override
  protected ServiceResult internalDoService(AddOrderArg arg) throws Exception {
    try (StrolchTransaction tx = openTx(arg.realm)) {
      AddOrderCommand command = new AddOrderCommand(tx);
      command.setOrder(arg.order);
      tx.addCommand(command);
      tx.commitOnClose();
    }
    return ServiceResult.success();
  }

  public static class AddOrderArg extends ServiceArgument {
    public Order order;
  }
}
```

**AddOrderCommand**:

```java
public class AddOrderCommand extends Command {

  private Order order;

  public AddOrderCommand(StrolchTransaction tx) {
    super(tx);
  }

  public void setOrder(Order order) {
    this.order = order;
  }

  @Override
  public void validate() {
    DBC.PRE.assertNotNull("Order may not be null!", this.order);
  }

  @Override
  public void doCommand() {
    tx().lock(this.order);

    OrderMap orderMap = tx().getOrderMap();
    if (orderMap.hasElement(tx(), this.order.getType(), this.order.getId())) {
      throw new StrolchUserMessageException("The Order already exists!");
    }

    orderMap.add(tx(), this.order);
  }

  @Override
  public void undo() {
    if (this.order != null && tx().isRollingBack()) {
      OrderMap orderMap = tx().getOrderMap();
      if (orderMap.hasElement(tx(), this.order.getType(), this.order.getId()))
        orderMap.remove(tx(), this.order);
    }
  }
}
```

### Generic CRUD Services

Strolch provides a set of generic services for standard operations:

*   `AddResourceService`, `UpdateResourceService`, `RemoveResourceService`
*   `AddOrderService`, `UpdateOrderService`, `RemoveOrderService`
*   `AddOrUpdateStrolchRootElementService`: Handles adding or updating any root element automatically.