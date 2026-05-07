# Session Handle

## Capability
- `SessionHandle`: Linear (Ensures that every open session is explicitly closed).

## Purpose
User session control with guaranteed closure. Prevents session leaks and state inconsistencies in the backend by requiring each session handle to be consumed by the termination function.

## Usage Example
```austral
import session (
    SessionHandle,
    CreateSession,
    EndSession
);

-- Example
let sess: SessionHandle := CreateSession(42);
EndSession(sess);
```

## Tests
To run the tests:
```bash
make test
```
