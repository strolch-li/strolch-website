---
title: 'Policies'
weight: 100
---

## Policies

Policies are an integral part when writing business logic in Strolch. In many
cases it would suffice to write all such logic in `Services` and `Commands`, but
as soon as behaviour can change, depending on the element being accessed, then
this would quickly lead to many if/else blocks.

Since writing large if/else blocks is not maintanable in the long run, Strolch
offers a different approach. All Strolch elements can store Policy definitions.
This is a simple key/value store where the key defines the type of policy, and
the value references the policy to use.

Currently there are two ways to reference a policy in Strolch, either via a key
which defines a further lookup in the `PolicyHandler`, or directly as the name
of the class to instantiate.

Using policies in Strolch gives the additional possibility of easily changing
the behaviour at runtime, as a Service and/or Command would delegate the
behaviour to the currently configured policy on the releveant element.

Policies are implemented by defining an abstract class and extends
StrolchPolicy. This abstract class then defines the API of the actual policy. A
concrete class then extends this abstract class and implements the concrete
methods.

Policies are registered on Resources, Orders, Activities and Actions. The
following shows defining two policies on a Resource, a PlanningPolicy, an
ExecutionPolicy in XML:

```xml
<Resource Id="myResource" Name="My Resource" Type="MyType">
  ...
  <Policies>
    <Policy Type="PlanningPolicy" Value="key:SimplePlanning" />
    <Policy Type="ExecutionPolicy" Value="java:li.strolch.policytest.TestSimulatedExecutionPolicy" />
  </Policies>
</Resource>
```

{{% notice tip %}} Note how the `PlanningPolicy` has a value of `key:SimplePlanning`
and the `ExecutionPolicy` defines a reference to an actual class. {{% /notice %}}

To use the `PolicyHandler`, it must be configured in the `StrolchConfiguration.xml`
as follows:

```xml
<StrolchConfiguration>
    <env id="dev">
        <Component>
            <name>PolicyHandler</name>
            <api>li.strolch.policy.PolicyHandler</api>
            <impl>li.strolch.policy.DefaultPolicyHandler</impl>
            <Properties>
                <readPolicyFile>true</readPolicyFile>
                <policyConfigFile>StrolchPolicies.xml</policyConfigFile>
            </Properties>
        </Component>
    </env>
</StrolchConfiguration>
```

And this policy handler implementation requires a file where the lookups for the
policies is defined, e.g.:

```xml
<StrolchPolicies>
    <PolicyType Type="PlanningPolicy" Api="li.strolch.policytest.TestPlanningPolicy">
        <Policy Key="SimplePlanning" Class="li.strolch.policytest.TestSimplePlanningPolicy"/>
    </PolicyType>
    <PolicyType Type="ExecutionPolicy" Api="li.strolch.execution.policy.ExecutionPolicy">
        <Policy Key="SimulatedExecution" Class="li.strolch.execution.policy.RandomDurationExecution"/>
    </PolicyType>
    <PolicyType Type="ConfirmationPolicy" Api="li.strolch.policytest.TestConfirmationPolicy">
        <Policy Key="NoConfirmation" Class="li.strolch.policytest.TestNoConfirmationPolicy"/>
    </PolicyType>
</StrolchPolicies>
```

Now at runtime we can access the policies:

```java
public class MyService extends AbstractService<ServiceArgument, ServiceResult> {
	@Override
	protected ServiceResult internalDoService(ServiceArgument arg) throws Exception {
		try (StrolchTransaction tx = openArgOrUserTx(arg)) {
			Resource res = tx.getResourceBy("MyType", "myTestResource");

			PolicyDef planningPolicyDef = res.getPolicyDef(PlanningPolicy.class);
			PlanningPolicy planningPolicy = tx.getPolicy(planningPolicyDef);
			planningPolicy.plan(...);

			PolicyDef executionPolicyDef = res.getPolicyDef(ExecutionPolicy.class);
			ExecutionPolicy executionPolicy = tx.getPolicy(executionPolicyDef);
			executionPolicy.toExecution(...);

			tx.commitOnClose();
		}

		return ServiceResult.success();
	}
}
```
