---
title: Strolch
---

<p align="center">
  <img src="assets/images/strolch_mascot_hero.svg" alt="Strolch Mascot" width="400">
</p>

# The Agile Parameterized Framework

Strolch is a unique Java framework designed for rapid development of robust, data-centric applications. It moves away from traditional Object-Relational Mapping (ORM) and instead uses a powerful abstract model that fits almost any domain.

### Why Strolch?

*   **⚡ Rapid Development**: Define your data model in XML and start building business logic immediately.
*   **🧩 Simple Abstract Model**: Everything is a Resource, an Order, or an Activity.
*   **💾 Flexible Persistence**: Transparently switch between PostgreSQL, XML, or even transient in-memory storage.
*   **🔒 Built-in Security**: Multi-client (realm) support and fine-grained privilege handling are core features.
*   **🚀 In-Memory Speed**: Enjoy the performance of in-memory data access with the reliability of persisted storage.

---

### Core Concepts

At the heart of Strolch are three fundamental elements:

1.  **Resources**: Represent static data like products, users, or machines.
2.  **Orders**: Represent dynamic data like customer orders, tasks, or logs.
3.  **Activities**: Represent workflows and processes, from simple steps to complex hierarchies.

Every element is **parameterized**, meaning you can add new fields (Parameters) grouped in Bags at any time without changing your database schema or Java classes.

```xml
<Resource Id="myMachine" Name="Drilling Machine" Type="Machine">
    <ParameterBag Id="parameters" Name="Parameters" Type="Parameters">
        <Parameter Id="serial" Name="Serial Number" Type="String" Value="SN-12345"/>
        <Parameter Id="weight" Name="Weight" Type="Float" Value="150.5"/>
    </ParameterBag>
</Resource>
```

---

### Key Features

*   **Transactions**: Robust transaction management with pessimistic locking.
*   **Search API**: Fluent API for querying your data model efficiently.
*   **Service & Command Pattern**: Encapsulate your business logic in reusable, testable units.
*   **Policy-Based Logic**: Easily swap algorithms at runtime using the Policy pattern.
*   **Reporting**: Powerful reporting engine that works directly on your model elements.
*   **REST & OpenAPI**: Fully documented REST API for remote access.
*   **Admin UI**: Ready-to-use WebComponents for data inspection, user management, and more.

---

### Get Started in Minutes

Ready to dive in? Check out our [Tutorial]({{< relref "/tutorial" >}}) to build your first Strolch application, or explore the [Documentation]({{< relref "/documentation" >}}) for a deep dive into the framework.

{{% notice info %}}
Strolch is actively developed and used in production environments ranging from industrial PLC control systems to high-level ERP integrations.
{{% /notice %}}

[**Learn more about the history of Strolch**]({{< relref "/history" >}})