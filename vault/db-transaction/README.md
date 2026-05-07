# DB Transaction (Linear)

This package ensures that database transactions are handled atomically and safely through linear types.

## Linearity
The compiler forces the developer to call `Commit` or `Rollback`. If the transaction is ignored, the code will not compile, avoiding hanging connections or inconsistent states.

## Usage
```austral
import Austral.Vault.DB (Transaction, BeginTransaction, Commit, Rollback);

let tx: Transaction := BeginTransaction();
-- Success path
Commit(tx);
-- OR Rollback(tx);
```
