# Secret Handle (Linear)

This package manages sensitive secrets using linear types to ensure they are wiped from memory after use.

## Linearity
Linearity ensures that every accessed secret is mandatory "wiped" (`WipeSecret`), preventing sensitive information from remaining in memory longer than necessary.

## Usage
```austral
import Austral.Vault.Secret (SecretHandle, AccessSecret, RevealSecret, WipeSecret);

let s: SecretHandle := AccessSecret("api-key");
let val: String := RevealSecret(&s);
WipeSecret(s);
```
