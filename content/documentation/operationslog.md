---
title: 'Operations Log'
weight: 130
---

## Operations Log

The `OperationsLog` component provides a centralized way to log operational messages within a Strolch application. These messages are typically more high-level than technical logs and are intended for operators or administrators.

### Features

-   **Contextual Logging**: Messages are associated with a realm and optionally a `Locator`.
-   **Severity Levels**: Supports different levels such as `INFO`, `WARN`, `ERROR`, and `FATAL`.
-   **State Management**: Messages can have states (e.g., `Information`, `Active`, `Done`).
-   **Persistence**: Messages can be persisted to the database.
-   **In-Memory Buffer**: Maintains recent messages in memory for quick access.
-   **Mail Notifications**: Can be configured to send email notifications for certain types of messages.

### Adding a Message

To add a message to the operations log:

```java
OperationsLog operationsLog = agent.getComponent(OperationsLog.class);

LogMessage message = new LogMessage(
    realm,
    username,
    locator, // Optional element locator
    LogSeverity.Error,
    LogMessageState.Active,
    ResourceBundle.getBundle("my-bundle"),
    "error.key"
).withException(e);

operationsLog.addMessage(message);
```

### Configuration

The `OperationsLog` component can be configured in `StrolchConfiguration.xml`.

```xml
<Component>
    <name>OperationsLog</name>
    <api>li.strolch.handler.operationslog.OperationsLog</api>
    <impl>li.strolch.handler.operationslog.OperationsLog</impl>
    <Properties>
        <maxMessages>1000</maxMessages>
        <sendMails>true</sendMails>
        <sendMailsMinSeverity>Error</sendMailsMinSeverity>
        <sendMailsRecipients>admin@example.com</sendMailsRecipients>
    </Properties>
</Component>
```
