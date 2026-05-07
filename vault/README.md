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
