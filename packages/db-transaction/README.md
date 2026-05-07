# DB Transaction

## Capability
- `Transaction`: Linear (Ensures that every open transaction is explicitly finalized via Commit or Rollback).

## Purpose
Secure database transaction management. Through linear typing, the compiler prevents a transaction from being left open, forcing the developer to handle the success or failure of the process.

## Usage Example
```austral
import db (
    Transaction,
    BeginTransaction,
    Commit,
    Rollback
);

-- Example
let tx: Transaction := BeginTransaction();
-- ... logic
Commit(tx);
```

## Tests
To run the tests:
```bash
make test
```
