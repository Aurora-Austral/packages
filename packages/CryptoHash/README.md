# 🔐 Crypto Hash

Cryptographically secure hashing with linear types for Aurora Austral.

## Features

- **SHA-256** - 256-bit secure hash
- **SHA-512** - 512-bit secure hash  
- **HMAC-SHA256** - Hash-based message authentication
- **Linear Types** - Sensitive data is consumed and cannot leak
- **Constant-Time Comparison** - Prevents timing attacks

## Why Linear Types?

```austral
-- SecretData is Linear - must be consumed
let password: SecretData := makeSecretData("my-password");
let hash: Hash256 := sha256(password);
-- password is now consumed, cannot be used again
-- This prevents accidental data leaks!
```

## Usage

### Basic Hashing

```austral
import CryptoHash (
    makeSecretData,
    sha256,
    hash256ToHex
);

-- Hash a password
let password: SecretData := makeSecretData("user-password");
let hash: Hash256 := sha256(password);
let hexHash: String := hash256ToHex(hash);

printLn(hexHash); -- Output: hex string
```

### Comparing Hashes (Constant-Time)

```austral
import CryptoHash (
    compareHash256
);

let hash1: Hash256 := sha256(makeSecretData("password"));
let hash2: Hash256 := sha256(makeSecretData("password"));

if compareHash256(hash1, hash2) then
    printLn("Hashes match!");
end if;
```

### HMAC (Message Authentication)

```austral
import CryptoHash (
    hmacSha256
);

let key: SecretData := makeSecretData("secret-key");
let message: SecretData := makeSecretData("important message");
let mac: Hash256 := hmacSha256(key, message);
```

## Security Features

### 1. Linear Types Prevent Leaks

```austral
let secret: SecretData := makeSecretData("sensitive");
let hash1: Hash256 := sha256(secret);
-- let hash2: Hash256 := sha256(secret); -- ERROR! secret already consumed
```

### 2. Constant-Time Comparison

Prevents timing attacks by always comparing all bytes:

```austral
-- This takes the same time regardless of where hashes differ
if compareHash256(hash1, hash2) then
    -- Hashes match
end if;
```

### 3. Automatic Memory Cleanup

When `SecretData` is consumed, memory is automatically deallocated:

```austral
function sha256(data: SecretData): Hash256 is
    -- ... hashing logic ...
    deallocate(data.data, data.length); -- Automatic cleanup
    return hash;
end;
```

## API Reference

### Types

```austral
-- Linear type - must be consumed
record SecretData: Linear is
    data: Pointer[Nat8];
    length: Nat64;
end;

-- Free type - can be copied
record Hash256: Free is
    bytes: FixedArray[Nat8, 32];
end;

record Hash512: Free is
    bytes: FixedArray[Nat8, 64];
end;
```

### Functions

```austral
-- Create secret data from string
function makeSecretData(content: String): SecretData;

-- Hash functions (consume SecretData)
function sha256(data: SecretData): Hash256;
function sha512(data: SecretData): Hash512;

-- Constant-time comparison
function compareHash256(a: Hash256, b: Hash256): Bool;
function compareHash512(a: Hash512, b: Hash512): Bool;

-- Convert to hex
function hash256ToHex(hash: Hash256): String;
function hash512ToHex(hash: Hash512): String;

-- HMAC
function hmacSha256(key: SecretData, message: SecretData): Hash256;
```

## Use Cases

### Password Hashing

```austral
function hashPassword(password: String): Hash256 is
    let secret: SecretData := makeSecretData(password);
    return sha256(secret);
end;

function verifyPassword(password: String, storedHash: Hash256): Bool is
    let secret: SecretData := makeSecretData(password);
    let hash: Hash256 := sha256(secret);
    return compareHash256(hash, storedHash);
end;
```

### API Token Generation

```austral
function generateToken(userId: String, timestamp: String): String is
    let data: String := concat(userId, timestamp);
    let secret: SecretData := makeSecretData(data);
    let hash: Hash256 := sha256(secret);
    return hash256ToHex(hash);
end;
```

### Message Authentication

```austral
function signMessage(key: String, message: String): Hash256 is
    let keyData: SecretData := makeSecretData(key);
    let msgData: SecretData := makeSecretData(message);
    return hmacSha256(keyData, msgData);
end;

function verifyMessage(key: String, message: String, signature: Hash256): Bool is
    let keyData: SecretData := makeSecretData(key);
    let msgData: SecretData := makeSecretData(message);
    let computed: Hash256 := hmacSha256(keyData, msgData);
    return compareHash256(computed, signature);
end;
```

## Building

```bash
make compile  # Build library
make test     # Run tests
make clean    # Clean build artifacts
```

## Testing

```bash
make test
```

Output:
```
Testing Crypto Hash Module...
Testing SHA-256...
Hash: a1b2c3d4...
✓ SHA-256: Same input produces same hash
✓ SHA-256: Different input produces different hash
Testing SHA-512...
✓ SHA-512: Same input produces same hash
Testing constant-time comparison...
✓ Comparison: Equal hashes detected
✓ Comparison: Different hashes detected
Testing HMAC-SHA256...
✓ HMAC: Same key+message produces same HMAC
✓ HMAC: Different key produces different HMAC
All tests passed!
```

## Security Notes

⚠️ **Important**: This is a demonstration implementation. For production use:

1. Use a battle-tested crypto library (libsodium, OpenSSL)
2. Add proper key derivation (PBKDF2, Argon2)
3. Use salts for password hashing
4. Consider using bcrypt/scrypt for passwords
5. Implement proper random number generation

## Installation

```bash
aurora install crypto-hash
```

## License

Apache-2.0

## Related Packages

- `secret-handle` - Secure secret management
- `session-handle` - Session token management
- `dpop-token` - DPoP token generation
