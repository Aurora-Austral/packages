# Secret Handle

## Capability
- `SecretHandle`: Linear (Ensures that sensitive data is wiped from memory after use).

## Purpose
Secret management with forced cleanup. The compiler guarantees that the developer calls the cleanup function (`WipeSecret`), preventing secrets from lingering in memory longer than necessary.

## Usage Example
```austral
import secret (
    SecretHandle,
    AccessSecret,
    RevealSecret,
    WipeSecret
);

-- Example
let handle: SecretHandle := AccessSecret(101);
let secret: Nat64 := RevealSecret(&handle);
WipeSecret(handle);
```

## Tests
To run the tests:
```bash
make test
```

@author: github.com/suissa
