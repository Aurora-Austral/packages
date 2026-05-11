# Linear Balance (Linear)

Este pacote trata valores monetários como tipos lineares, garantindo que o dinheiro nunca seja duplicado (double spending) ou perdido.

## Linearidade
Para mover valor, você deve consumir o saldo original e criar novos saldos. Isso reflete a conservação de valor no nível do sistema de tipos.

## Uso
```austral
import Austral.Vault.Finance (Balance, BalancePair, InitialBalance, Split, Merge, Spend);

let b: Balance := InitialBalance(100);
let pair: BalancePair := Split(b);
ConsumePair(pair);
```
