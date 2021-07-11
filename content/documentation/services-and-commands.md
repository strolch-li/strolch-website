---
title: 'Services and Commands'
weight: 70
---

## Services and Commands
`Services` are written to implement a specific use-case. `Commands` are written to 
implement re-usable parts of a use-case. The use-case can be abstract 
e.g., `AddResourceService` or very specific e.g. `CreatePatientService`.

Should the use-case be re-usable in different scenarios, then commands should 
implement the logic, and the services should then execute the commands. E.g. 
The `CreatePatientService` would use a `CreatePatientResourceCommand` and then 
use an `AddResourceCommand` in a single transaction, so that the task of 
creating the actual Patient Resource can be re-used somewhere else.

Services extend the abstract class `AbstractService` and then implement the 
method `internalDoService(ServiceArgument)`. AbstractService defines generic 
template arguments with which the concrete service can define a specific 
input ServiceArgument class and output ServiceResult class.

The AbstractService class has multiple helper methods:
* `openTx():StrolchTransaction` - to open a transaction
* `runPrivileged()` - to perform a `SystemUserAction`
* `getComponent():V` - to retrieve a specific StrolchComponent

there are more - check the JavaDocs.

Commands extend the `Command` class and then implement the method `doCommand()`. 
Commands have helper methods:
* `tx()` - to get the current transaction
* `getPolicy()` - to retrieve a `StrolchPolicy` instance
* `runPrivileged()` - to perform a `SystemUserAction`

there are more - check the JavaDocs.

The following code snippets shows how a Service and Command are used to 
perform the task of adding a new Order. Note how:
* the Service opens the transaction
* adds the command to the TX
* calls `tx.commitOnClose()`
* the command validates its input
* locks the object
* performs the work
* and implements an undo

AddOrderService:
```java
public class AddOrderService extends AbstractService<AddOrderService.AddOrderArg, ServiceResult> {

  @Override
  protected ServiceResult getResultInstance() {
    return new ServiceResult();
  }

  @Override
  protected ServiceResult internalDoService(AddOrderArg arg) {

    try (StrolchTransaction tx = openTx(arg.realm)) {
      AddOrderCommand command = new AddOrderCommand(getContainer(), tx);
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

AddOrderCommand:
```java
public class AddOrderCommand extends Command {

  private Order order;

  public AddOrderCommand(ComponentContainer container, StrolchTransaction tx) {
    super(container, tx);
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
      String msg = MessageFormat.format("The Order {0} already exists!", this.order.getLocator());
      throw new StrolchException(msg);
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