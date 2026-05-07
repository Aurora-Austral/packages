# DB Transaction (Linear)

Este pacote garante que as transações de banco de dados sejam tratadas de forma atômica e segura através de tipos lineares.

## Linearidade
O compilador obriga o desenvolvedor a chamar `Commit` ou `Rollback`. Se a transação for ignorada, o código não compila, evitando conexões pendentes ou estados inconsistentes.

## Uso
```austral
import Austral.Vault.DB (Transaction, BeginTransaction, Commit, Rollback);

let tx: Transaction := BeginTransaction();
Commit(tx);
```
