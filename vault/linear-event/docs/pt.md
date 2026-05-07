# Linear Event (Linear)

Este pacote garante que eventos críticos sejam processados e reconhecidos (ACK) exatamente uma vez.

## Linearidade
Útil em arquiteturas orientadas a eventos para garantir que nenhuma mensagem seja perdida ou deixada sem resposta pelo sistema.

## Uso
```austral
import Austral.Vault.Event (LinearEvent, ReceiveEvent, Acknowledge);

let ev: LinearEvent := ReceiveEvent("payload");
Acknowledge(ev);
```
