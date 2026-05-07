# Linear Balance

## Capability
- `Balance`: Linear (Prevents accidental creation or destruction of financial values).
- `BalancePair`: Linear (Result of the Split operation, must also be consumed).

## Purpose
Implementation of financial balances with conservation guarantee. In Austral, you cannot simply delete a balance; it must be spent, merged, or split, ensuring the sum of the values remains intact.

## Usage Example
```austral
import balance (
    Balance,
    InitialBalance,
    Split,
    Merge,
    Spend
);

-- Example
let b: Balance := InitialBalance(100);
let pair: BalancePair := Split(b);
-- ...
ConsumePair(pair);
```

## Tests
To run the tests:
```bash
make test
```

@author: github.com/suissa
