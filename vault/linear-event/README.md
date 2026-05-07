# Linear Event (Linear)

This package ensures that critical events are processed and acknowledged (ACK) exactly once.

## Linearity
Useful in event-driven architectures to ensure no message is lost or left unanswered by the system.

## Usage
```austral
import Austral.Vault.Event (LinearEvent, ReceiveEvent, Acknowledge);

let ev: LinearEvent := ReceiveEvent("event-payload");
Acknowledge(ev);
```
