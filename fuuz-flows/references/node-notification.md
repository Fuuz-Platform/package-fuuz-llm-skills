# Notification

Nodes for sending and receiving messages through notification channels.

---

### Receive Notification (`receiveNotification`)
**Type:** source

A node used to receive notifications from a notification channel.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `notificationChannel` | string | | Y | The notification channel to subscribe to. |

Standard output port.

---

### Send Notification (`sendNotification`)
**Type:** transition

A node used send to messages to a notification channel.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `notificationChannelId` | string | | Y | The notification channel to send messages to. |
| `transform` | string | jsonata | N | A transform producing an array of notifications to send. Default: `"{}"` |

Standard output port.
