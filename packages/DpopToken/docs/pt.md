# DPoP Token (Austral)

Este pacote fornece uma implementação robusta e segura para o gerenciamento de tokens **DPoP (Demonstrating Proof-of-Possession)** utilizando a linguagem **Austral**.

## Por que Austral e Tipos Lineares?

A principal característica deste pacote é a utilização de **Tipos Lineares (`Linear`)**. Em Austral, um tipo linear garante que um recurso seja utilizado **exatamente uma vez**.

### Benefícios no contexto de Segurança:

1.  **Garantia de Destruição:** Como o `DPoPToken` é linear, o compilador obriga o desenvolvedor a consumi-lo (através da função `DestroyToken`). Isso impede que tokens fiquem "orfãos" na memória.
2.  **Prevenção de Vazamentos:** Um token linear não pode ser descartado silenciosamente ou copiado. Se você tentar ignorar um token criado, o código não compilará.
3.  **Rastreabilidade de Posse:** A linearidade reflete a natureza do DPoP, onde a posse e o uso do token devem ser estritamente controlados.

## Uso

```austral
import Austral.Vault.DPoP (DPoPToken, CreateToken, GetContent, DestroyToken);

let token: DPoPToken := CreateToken("meu-jwt-dpop");
let c: String := GetContent(&token);
DestroyToken(token);
```
