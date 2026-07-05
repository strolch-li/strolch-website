---
title: 'Audits'
weight: 150
---

## Audit Handler

The `AuditHandler` component is responsible for recording security-relevant events and data changes within a Strolch application.

### Features

-   **Data Auditing**: Automatically records changes to Resources, Orders, and Activities when performed within a transaction.
-   **Service Auditing**: Records executions of Strolch Services.
-   **Search Auditing**: Records search queries executed against the Strolch model.
-   **Asynchronous Processing**: Audits are typically processed in the background.

### Audit Record

An audit record contains:
-   **Username**: The user who performed the action.
-   **Timestamp**: When the action occurred.
-   **Action**: The name of the action performed.
-   **Element**: The type and ID of the element affected.
-   **Access Type**: The type of access (`READ`, `CREATE`, `UPDATE`, `DELETE`).

### Auditing in Transactions

Auditing is usually handled automatically by the `StrolchTransaction`.

```java
try (StrolchTransaction tx = agent.openTx(certificate, "MyAction", false)) {
    // Operations here will be audited
    tx.commitOnClose();
}
```

Auditing can be suppressed for a specific transaction:

```java
try (StrolchTransaction tx = agent.openTx(certificate, "NonAuditedAction", false)) {
    tx.suppressAudits();
    // ...
}
```

### Configuration

The `AuditHandler` is configured in `StrolchConfiguration.xml`.

```xml
<Component>
    <name>AuditHandler</name>
    <api>li.strolch.handler.audits.AuditHandler</api>
    <impl>li.strolch.handler.audits.AuditHandler</impl>
    <depends>RealmHandler</depends>
</Component>
```
