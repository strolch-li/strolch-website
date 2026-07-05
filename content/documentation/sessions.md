---
title: 'Sessions'
weight: 170
---

## Sessions and Privileges

Strolch provides a robust security model for authentication and authorization, primarily managed by the `StrolchSessionHandler` and `PrivilegeHandler`.

### Privilege Handler

The `PrivilegeHandler` is the core component for security. It delegates to the `strolch-privilege` library to:
-   **Authenticate Users**: Validate credentials (username/password, SSO data).
-   **Manage Certificates**: Issue and validate `Certificate` objects that represent an active session.
-   **Authorize Actions**: Check if a user has the required privileges to perform a specific action.
-   **Run As**: Execute code with the privileges of a specific user or as a system agent.

### Strolch Session Handler

The `StrolchSessionHandler` manages user sessions at the agent level:
-   **Session Lifecycle**: Handles session creation, validation, and invalidation (logout).
-   **Session Metadata**: Tracks information about active sessions (source IP, login time).
-   **Timeout Management**: Automatically invalidates sessions after a period of inactivity.

### Key Concepts

-   **`Certificate`**: A token issued upon successful authentication. It must be passed to most Strolch APIs.
-   **`PrivilegeContext`**: Provides information about the current user's privileges.

### Authentication Example

```java
StrolchSessionHandler sessionHandler = agent.getComponent(StrolchSessionHandler.class);
Certificate certificate = sessionHandler.authenticate("username", "password".toCharArray(), "source-ip", Usage.AUTHENTICATE, false);
```

### Authorization in Transactions

When a transaction is opened with a certificate, operations are checked against the user's privileges.

```java
try (StrolchTransaction tx = agent.openTx(certificate, "SensitiveAction", false)) {
    // If the user lacks 'SensitiveAction' privilege, an exception will be thrown
    tx.commitOnClose();
}
```

### Running as Agent

For background tasks, you can run code with system privileges.

```java
agent.runAsAgent(tx -> {
    // This code runs with full system privileges
    tx.commitOnClose();
});
```
