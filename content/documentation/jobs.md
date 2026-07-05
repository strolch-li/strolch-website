---
title: 'Jobs'
weight: 120
---

## Strolch Job Handler

The `StrolchJobsHandler` manages background tasks and scheduled jobs within the Strolch Agent.

### StrolchJob

A `StrolchJob` is a background task that can be executed periodically or on demand. It provides methods for:
*   **Scheduling**: Define execution intervals using delays or CRON expressions.
*   **Execution**: Logic is implemented in the `execute` method.
*   **Lifecycle**: Jobs are registered with the `StrolchJobsHandler`.
*   **Privileges**: Jobs run with specific privileges, typically as a system user or agent.

### Implementing a Job

To create a new job, extend the `StrolchJob` class and implement the `doExecute` method.

```java
public class MyJob extends StrolchJob {
    public MyJob(StrolchAgent agent, String id, String name, JobMode mode) {
        super(agent, id, name, mode);
    }

    @Override
    protected void doExecute(PrivilegeContext ctx) throws Exception {
        runAsAgent(tx -> {
            // Perform background work in a transaction
            tx.commitOnClose();
        });
    }
}
```

### Job Modes

*   **`JobMode.Manual`**: The job must be triggered manually.
*   **`JobMode.Recurring`**: The job runs periodically based on a defined delay.
*   **`JobMode.Cron`**: The job runs according to a CRON expression.

### Scheduling a Job

Jobs can be scheduled programmatically or via configuration.

```java
StrolchJobsHandler jobsHandler = agent.getComponent(StrolchJobsHandler.class);
MyJob job = jobsHandler.register(MyJob.class);
job.setDelay(1, TimeUnit.MINUTES, 5, TimeUnit.MINUTES);
job.schedule();
```

### Model-based Configuration

Jobs can also be configured as Strolch `Resource` elements in the model. This allows for dynamic job registration and configuration without code changes.

A job resource must have the `Type` set to `StrolchJob`.

**Example XML Configuration**:

```xml
<Resource Id="expiredProductsJob" Name="Expired Products Job" Type="StrolchJob">
    <ParameterBag Id="parameters" Name="Parameters" Type="Parameters">
        <Parameter Id="className" Name="Class Name" Type="String" Value="com.example.MyJob"/>
        <Parameter Id="mode" Name="Job Mode" Type="String" Interpretation="Enumeration" Uom="JobMode" Value="Recurring"/>
        <Parameter Id="delay" Name="Delay" Type="Integer" Interpretation="TimeUnit" Uom="MINUTES" Value="30"/>
        <Parameter Id="initialDelay" Name="Initial Delay" Type="Integer" Interpretation="TimeUnit" Uom="SECONDS" Value="10"/>
    </ParameterBag>
</Resource>
```

### Monitoring

The `StrolchJobsHandler` provides information about job status, last execution time, duration, and any exceptions that occurred.
