---
title: 'Architecture'
weight: 50
---

## Architecture

### Overview

![Strolch PLC Architecture Overview](/assets/images/Strolch-PLC-Architecture-Overview.png)

The Strolch PLC architecture sees the Strolch Agent as the server, managing
logical devices, i.e. multiple sensors and actors together and thus deciding on
further steps. With this architecture multiple PLCs can be combined together in
one agent for flow control.

### PLC Architecture

![Strolch PLC Architecture](/assets/images/Strolch-PLC-Architecture.png)

On the agent side the two main classes are the `PlcGwServerHandler` and the
`PlcGwService`

The `PlcGwServerHandler` handles connections from remote PLCs over WebSockets and
sends the requests to these PLCs. A `PlcGwService` instance will be notified and
can then decide on an action. In an execution model with Activities, the
`PlcNotificationListener` interface can be implemented, or the `PlcExecutionPolicy`
can be directly extended.

On the PLC side, the `PlcGwClientHandler` is optional if no agent is required. The
`PlcHandler` initializes the model and connections. The `Plc` class is Strolch
agnostic and manages the connections and notifies `PlcListener` instances on
changes coming from the underlying connections. The `PlcService` implementations
implement business logic, and can also be notified on updates from connections.

