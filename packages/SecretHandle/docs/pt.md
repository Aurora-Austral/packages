# Secret Handle (Linear)

Este pacote gerencia segredos sensíveis usando tipos lineares para garantir que sejam removidos da memória após o uso.

## Linearidade
A linearidade garante que cada segredo acessado seja obrigatoriamente "limpo" (`WipeSecret`), evitando que informações sensíveis permaneçam na memória além do necessário.

## Uso
```austral
import Austral.Vault.Secret (SecretHandle, AccessSecret, RevealSecret, WipeSecret);

let s: SecretHandle := AccessSecret("api-key");
let val: String := RevealSecret(&s);
WipeSecret(s);
```
