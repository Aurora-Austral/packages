# Linear Event

## Capability
- `LinearEvent`: Linear (Ensures that every received event is explicitly processed or acknowledged).

## Purpose
Event system where processing is guaranteed by the compiler. Ideal for messaging systems where "At-Least-Once" or "Exactly-Once" is critical at the code level.

## Usage Example
```austral
import event (
    LinearEvent,
    ReceiveEvent,
    Acknowledge
);

-- Example
let ev: LinearEvent := ReceiveEvent(12345);
Acknowledge(ev);
```

## Tests
To run the tests:
```bash
make test
```

@author: github.com/suissa
