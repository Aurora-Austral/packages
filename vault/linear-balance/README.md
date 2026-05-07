# Linear Balance (Linear)

This package treats monetary values as linear types, ensuring money is never duplicated (double spending) or lost.

## Linearity
To move value, you must consume the original balance and create new balances. This reflects value conservation at the type system level.

## Usage
```austral
import Austral.Vault.Finance (Balance, BalancePair, InitialBalance, Split, Merge, Spend);

let b: Balance := InitialBalance(100);
let pair: BalancePair := Split(b);
-- Use helper to consume
ConsumePair(pair);
```
