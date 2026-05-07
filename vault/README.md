<<<<<<< HEAD
# Aurora Vault Package Specifications (Austral 0.2.0)

This directory contains the packages for the Aurora Vault suite. To ensure compatibility with the Austral 0.2.0 compiler and architectural consistency, all new packages must strictly follow the specifications below.

## 1. Mandatory File Structure

Each package must be a directory containing:
- `name.aui`: Module interface.
- `name.aum`: Module implementation.
- `name.test.aum`: Test suite and entry point.
- `Makefile`: Build and test automation.
- `README.md`: Package-specific documentation (English).
- `docs/pt.md`: Detailed documentation in Portuguese.

---

## 2. Standards per File

### 2.1. Interface (`.aui`)
- **Namespace**: Use simple, lowercase identifiers (e.g., `module dpop is`). Avoid dots in module names to prevent parsing errors.
- **Linear Types**: Define security-critical types with `: Linear`.
- **References**: Always use region syntax with the type coming first: `&[Type, Region]`.
    - Correct: `function Get(t: &[Token, R]): Nat64;`
    - Incorrect: `function Get(t: &[R, Token]): Nat64;`

### 2.2. Implementation (`.aum`)
- **Record Syntax**: Record closure must be just `end;`.
    - Correct: `record T : Linear is ... end;`
    - Incorrect: `record T : Linear is ... end record;`
- **Field Access**: For references, you must use the pointer operator `->`.
    - Example: `return ref->field;`
- **Destructuring**: To rename fields in a `let`, use the `as` syntax.
    - Example: `let { field as local: Type } := value;`
- **Unit Return**: Functions returning `Unit` must use `return nil;`.

### 2.3. Tests (`.test.aum`)
- **Test Module**: Name it `name_test`.
- **Entry Point**: The `main` function must be generic and consume the `RootCapability`.
    ```austral
    generic function main(root: RootCapability): ExitCode is
        surrenderRoot(root);
        return ExitSuccess();
    end;
    ```
- **Visual Feedback**: Use `printLn` to confirm operation success in the terminal.

### 2.4. Makefile
The Makefile must support the `make test` command using the comma syntax for interface/body pairs:
```makefile
test:
	austral compile modulo.aui,modulo.aum teste.aum --entrypoint=modulo_test:main --output=binary
	./binary
```

### 2.5. README.md
The `README.md` must follow this exact pattern:
1.  **Title**: Just the package name (e.g., `# DPoP Token`).
2.  **Capability**: Section detailing linear types (e.g., `## Capability`).
3.  **Purpose**: What the package solves.
4.  **Usage Example**: Short snippet on how to import and use.
5.  **Tests**: Command to run the local suite.

---

## 3. Typing Considerations (Austral 0.2.0)
Until `String` support is standardized in the local environment, use `Nat64` for payloads and identifiers to ensure stable test compilation.
=======
# Aurora Vault Packages

This directory contains the highly secure packages comprising the **Aurora Vault** suite. 

## Available Packages
- **[db-transaction](./db-transaction)**: Secure database transaction management enforcing explicit Commit or Rollback via linear capability consumption.
- **[dpop-token](./dpop-token)**: DPoP pattern for access tokens with anti-replay guarantees and single-use enforcement.
- **[linear-balance](./linear-balance)**: Financial balance implementation with strict conservation guarantees (must be spent, merged, or split).
- **[linear-event](./linear-event)**: Event system guaranteeing that every received event is explicitly processed or acknowledged.
- **[secret-handle](./secret-handle)**: Secure memory management with forced wipe/cleanup of sensitive data before disposal.
- **[session-handle](./session-handle)**: User session control with guaranteed closure to prevent state leaks.

---

## Package Architecture & Development Standards

To ensure compatibility with the Austral 0.2.0 compiler and maintain architectural consistency, all packages within the Vault must strictly adhere to the following specifications:

### 1. Mandatory File Structure
Each package must reside in a dedicated folder (e.g., `vault/[package-name]`) and contain:
- `[name].aui`: Module interface.
- `[name].aum`: Module implementation.
- `[name].test.aum`: Test suite and entry point.
- `Makefile`: Build and test automation.
- `README.md`: Package-specific documentation (in English).
- `docs/pt.md`: Detailed documentation (in Portuguese).

### 2. File Standards

#### Interface (`.aui`)
- **Namespace**: Use simple, lowercase identifiers without dots (e.g., `module dpop is`) to prevent parsing errors.
- **Linear Types**: Define security-critical types with `: Linear`.
- **References**: Always use the explicit region syntax with the type coming first: `&[Type, Region]`.

#### Implementation (`.aum`)
- **Record Syntax**: Record closure must be exactly `end;` (do not use `end record;`).
- **Field Access**: When working with references, always use the pointer operator `->` (e.g., `ref->field`).
- **Destructuring**: To rename fields in a `let` binding, use the `as` syntax: `let { field as local: Type } := value;`.
- **Unit Return**: Functions returning `Unit` must explicitly `return nil;`.

#### Tests (`.test.aum`)
- The `main` function must be generic and successfully consume the `RootCapability` to satisfy linearity:
  ```austral
  generic function main(root: RootCapability): ExitCode is
      -- ... your tests ...
      surrenderRoot(root);
      return ExitSuccess();
  end;
  ```
- Always provide visual feedback in the terminal using `printLn`.

#### Makefile
The Makefile must support the `make test` command utilizing the comma-separated syntax for interface/body pairs:
```makefile
test:
	austral compile module.aui,module.aum module.test.aum --entrypoint=module_test:main --output=binary
	./binary
```

#### Package README.md
Every package's `README.md` must follow this exact template:
1. **Title**: Just the package name (e.g., `# DPoP Token`).
2. **Capability**: A section detailing the linear types and their specific security guarantees.
3. **Purpose**: A brief description of the problem the package solves.
4. **Usage Example**: A short Austral code snippet demonstrating import and usage.
5. **Tests**: The command to run the local test suite.
>>>>>>> 18db231 (logo)
