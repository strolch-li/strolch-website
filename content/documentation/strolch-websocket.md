---
title: 'WebSockets'
weight: 240
---

## Strolch WebSocket

The `strolch-websocket` module provides a real-time observation API for Strolch elements. It allows clients to receive updates when Resources, Orders, or Activities are added, updated, or removed.

### Endpoint

The default WebSocket endpoint is:

```
ws://<host>:<port>/<context>/websocket/strolch/observer
```

### Protocol

Communication is performed using JSON messages.

#### Authentication

Clients must authenticate immediately after connecting.

**Request:**
```json
{
  "msgType": "Authenticate",
  "username": "admin",
  "authToken": "..."
}
```

#### Observer Registration

After authentication, clients can register to observe changes for specific object types.

**Request:**
```json
{
  "msgType": "ObserverRegister",
  "objectType": "Resource",
  "type": "Ball",
  "realm": "strolch_admin"
}
```

- `objectType`: The Strolch element type (`Resource`, `Order`, `Activity`).
- `type`: The specific sub-type, or `*` for all types.

### Server Updates

When an observed element changes, the server sends an update message.

**Example Update:**
```json
{
  "msgType": "ObserverUpdate",
  "objectType": "Resource",
  "type": "Ball",
  "data": [
    {
      "id": "b1",
      "type": "Ball",
      ...
    }
  ]
}
```
