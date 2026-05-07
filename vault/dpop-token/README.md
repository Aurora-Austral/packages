# DPoP Token

## Capability
- `DPoPToken`: Linear (Ensures single-use consumption and prevents token leakage).

## Purpose
This package implements the DPoP (Demonstrating Proof-of-Possession) pattern for access tokens. It ensures that the token is consumed exactly once, utilizing Austral's linear type system to prevent replay attacks and misuse.

## Usage Example
```austral
import dpop (
    DPoPToken,
    CreateToken,
    GetContent,
    DestroyToken
);

-- Example
let token: DPoPToken := CreateToken(123);
let val: Nat64 := GetContent(&token);
DestroyToken(token);
```

## Tests
To run the tests:
```bash
make test
```
