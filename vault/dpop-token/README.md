# DPoP Token (Austral)

This package provides a robust and secure implementation for managing **DPoP (Demonstrating Proof-of-Possession)** tokens using the **Austral** language.

## Why Austral and Linear Types?

The core feature of this package is the use of **Linear Types (`Linear`)**. In Austral, a linear type ensures that a resource is used **exactly once**.

### Security Benefits:

1.  **Guaranteed Destruction:** Since `DPoPToken` is linear, the compiler forces the developer to consume it (via `DestroyToken`). This prevents "orphaned" tokens in memory.
2.  **Leak Prevention:** A linear token cannot be silently discarded or copied. If you try to ignore a created token, the code will not compile.
3.  **Possession Traceability:** Linearity reflects the nature of DPoP, where token possession and usage must be strictly controlled.

## Package Structure

-   `dpop.aui`: Module interface, defining the linear type and allowed operations.
-   `dpop.aum`: Implementation of operations and internal token structure.
-   `dpop.test.aum`: Test suite demonstrating the lifecycle (Creation -> Usage -> Destruction).
-   `Makefile`: Commands for compilation and running tests.

## Usage

```austral
import Austral.Vault.DPoP (DPoPToken, CreateToken, GetContent, DestroyToken);

-- In your code:
let token: DPoPToken := CreateToken("my-dpop-jwt");

-- Accessing content without consuming the token (using borrowing)
let c: String := GetContent(&token);

-- Ending the lifecycle (Mandatory because it's Linear)
DestroyToken(token);
```

## Compilation

To compile the package:
```bash
make
```

To run tests:
```bash
make test
```
