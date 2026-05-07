# Session Handle (Linear)

Este pacote gerencia o estado da sessão do usuário de forma segura, garantindo que o ciclo de vida da sessão seja encerrado corretamente.

## Linearidade
Força o desenvolvedor a gerenciar explicitamente o fim da sessão, evitando sessões pendentes no servidor ou estados inconsistentes no cliente.

## Uso
```austral
import Austral.Vault.Session (SessionHandle, CreateSession, EndSession);

let sess: SessionHandle := CreateSession("user-id");
EndSession(sess);
```
