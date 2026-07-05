---
title: 'Policies'
weight: 100
---

## Policies

Policies are an integral part when writing business logic in Strolch. In many cases it would suffice to write all such
logic in `Services` and `Commands`, but as soon as behaviour can change, depending on the element being accessed, then
this would quickly lead to many if/else blocks.

Since writing large if/else blocks is not maintainable in the long run, Strolch offers a different approach. All Strolch
elements can store Policy definitions. This is a simple key/value store where the key defines the type of policy, and
the value references the policy to use.

The `PolicyHandler` is Strolch's mechanism for dependency injection and extensibility. It allows defining
interchangeable logic that can be selected at runtime based on the data model.

### Concepts

- **Policy Definition (`PolicyDef`)**: A reference to a policy type and a key.
- **Policy Implementation (`StrolchPolicy`)**: The actual Java class that implements the logic.
- **Policy Configuration**: Policies are configured in an XML file (e.g., `StrolchPolicies.xml`) or within the element's
  XML definition.

Currently there are two ways to reference a policy in Strolch, either via a key which defines a further lookup in the
`PolicyHandler`, or directly as the name of the class to instantiate.

### Defining a Policy

Policies are registered on Resources, Orders, Activities and Actions. The following shows defining two policies on a
Resource, a PlanningPolicy and an ExecutionPolicy in XML:

```xml

<Resource Id="myResource" Name="My Resource" Type="MyType">
    ...
    <Policies>
        <Policy Type="PlanningPolicy" Value="key:SimplePlanning"/>
        <Policy Type="ExecutionPolicy" Value="java:li.strolch.policytest.TestSimulatedExecutionPolicy"/>
    </Policies>
</Resource>
```

{{% notice tip %}} Note how the `PlanningPolicy` has a value of `key:SimplePlanning` and the `ExecutionPolicy` defines a
reference to an actual class. {{% /notice %}}

### Implementing a Policy

To implement a policy, define an abstract class that extends `StrolchPolicy` and defines the API. A concrete class then
extends this and implements the logic.

```java
public class MyExecutionPolicy extends StrolchPolicy implements ExecutionPolicy {
	public MyExecutionPolicy(StrolchTransaction tx) {
		super(tx);
	}

	@Override
	public void execute(IActivityElement element) {
		// Implementation logic
	}
}
```

### Configuration

To use the `PolicyHandler`, it must be configured in the `StrolchConfiguration.xml`:

```xml

<Component>
    <name>PolicyHandler</name>
    <api>li.strolch.policy.PolicyHandler</api>
    <impl>li.strolch.policy.DefaultPolicyHandler</impl>
    <Properties>
        <readPolicyFile>true</readPolicyFile>
        <policyConfigFile>StrolchPolicies.xml</policyConfigFile>
    </Properties>
</Component>
```

The `StrolchPolicies.xml` file defines the lookups:

```xml

<StrolchPolicies>
    <PolicyType Type="PlanningPolicy" Api="li.strolch.policytest.TestPlanningPolicy">
        <Policy Key="SimplePlanning" Class="li.strolch.policytest.TestSimplePlanningPolicy"/>
    </PolicyType>
    <PolicyType Type="ExecutionPolicy" Api="li.strolch.execution.policy.ExecutionPolicy">
        <Policy Key="SimulatedExecution" Class="li.strolch.execution.policy.RandomDurationExecution"/>
    </PolicyType>
</StrolchPolicies>
```

### Using a Policy

Policies are retrieved within a transaction.

```java
try (StrolchTransaction tx = openArgOrUserTx(arg)) {
    Resource res = tx.getResourceBy("MyType", "myTestResource");

    PlanningPolicy planningPolicy = tx.getPolicy(res, PlanningPolicy.class);
    planningPolicy.plan(...);

    ExecutionPolicy executionPolicy = tx.getPolicy(res, ExecutionPolicy.class);
    executionPolicy.toExecution(...);

    tx.commitOnClose();
}
```

#### Retrieving a Policy without an Element

Sometimes you need a policy that is not directly attached to a model element. In this case, you can create a `PolicyDef`
manually.

```java
PolicyDef policyDef = PolicyDef.getKeyPolicy(ExecutionPolicy.class, "DefaultExecution");
ExecutionPolicy policy = tx.getPolicy(ExecutionPolicy.class, policyDef);
```

Alternatively, you can use `PolicyDef.valueOf()`:

```java
PolicyDef policyDef = PolicyDef.valueOf(ExecutionPolicy.class, "key:DefaultExecution");
```

Or reference a Java class directly:

```java
PolicyDef policyDef = PolicyDef.getJavaPolicy(ExecutionPolicy.class, MyExecutionPolicy.class);
```
