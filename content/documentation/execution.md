---
title: 'Execution'
weight: 180
---

## Execution Framework

The Strolch Execution Framework is a reactive engine for executing hierarchical business processes defined as `Activity` and `Action` elements.

### ExecutionHandler

The `ExecutionHandler` is the central component that manages the execution of activities. It tracks their state and triggers the associated execution policies.

### Activity and Action

*   **`Activity`**: A container for actions or other activities, defining a hierarchy.
*   **`Action`**: The atomic unit of work. It has a state (e.g., `Created`, `Planned`, `Execution`, `Stopped`, `Executed`) and references an `ExecutionPolicy`.

### Execution Policy

An `ExecutionPolicy` implements the actual logic for an action. It is called by the `ExecutionHandler` when the action's state changes.

```java
public class MyExecutionPolicy extends StrolchPolicy implements ExecutionPolicy {
    public MyExecutionPolicy(StrolchTransaction tx) {
        super(tx);
    }

    @Override
    public void toExecution(Action action) {
        // Start execution logic
        setStatus(action, State.EXECUTION);
    }

    @Override
    public void toExecuted(Action action) {
        // Finalize execution
        setStatus(action, State.EXECUTED);
    }
}
```

### Starting Execution

To start the execution of an activity:

```java
ExecutionHandler executionHandler = agent.getComponent(ExecutionHandler.class);
executionHandler.toExecution(realm, activityLocator);
```

### Execution Archiving

Completed activities can be automatically archived (moved to a separate storage or removed) to keep the operational model small and performant.
