# Session Handle (Linear)

This package manages user session state securely, ensuring the session lifecycle is properly closed.

## Linearity
Forces the developer to explicitly manage the session end, avoiding hanging sessions on the server or inconsistent states on the client.

## Usage
```austral
import Austral.Vault.Session (SessionHandle, CreateSession, EndSession);

let sess: SessionHandle := CreateSession("user-id");
EndSession(sess);
```
