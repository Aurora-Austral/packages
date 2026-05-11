# 🔄 Linear AutoDestroy

Automatic resource cleanup with RAII pattern for Aurora Austral.

## 🎯 Conceito

O módulo `linear-autodestroy` implementa o padrão **RAII (Resource Acquisition Is Initialization)** para Austral, onde recursos são **automaticamente destruídos** quando seu valor é extraído. O desenvolvedor não precisa chamar explicitamente funções de cleanup!

## 🚀 Por Que Usar?

### ❌ Sem AutoDestroy (Manual)
```austral
-- Você precisa lembrar de fechar
let file: FileHandle := openFile("data.txt");
let content: String := readFile(file);
let _ := closeFile(file); -- Fácil esquecer!
```

### ✅ Com AutoDestroy (Automático)
```austral
-- Fechamento automático!
let file: AutoFile := openAutoFile("data.txt");
let content: String := readAutoFile(file);
-- file é automaticamente fechado aqui!
```

## 📦 Features

- **🔄 Cleanup Automático** - Recursos destruídos automaticamente
- **🛡️ Zero Leaks** - Impossível esquecer de liberar recursos
- **🎯 Type-Safe** - Garantias em compile-time
- **🔒 Linear Types** - Recursos não podem ser duplicados
- **⚡ Zero Overhead** - Sem custo em runtime

## 🎨 Padrões Implementados

### 1. Auto-Destroy File

```austral
import LinearAutoDestroy (
    AutoFile,
    openAutoFile,
    readAutoFile
);

-- Arquivo é automaticamente fechado após leitura
let file: AutoFile := openAutoFile("config.txt");
let content: String := readAutoFile(file);
-- file foi destruído, não pode ser usado novamente!

printLn(content);
```

**Output:**
```
Opening file: config.txt
Reading file: config.txt
Auto-closing file: config.txt
File content from: config.txt
```

### 2. Auto-Destroy Connection

```austral
import LinearAutoDestroy (
    AutoConnection,
    connectAuto,
    queryAuto
);

-- Conexão é automaticamente fechada após query
let conn: AutoConnection := connectAuto("localhost:5432");
let result: String := queryAuto(conn, "SELECT * FROM users");
-- conn foi destruído!

printLn(result);
```

**Output:**
```
Connecting to: localhost:5432
Executing query: SELECT * FROM users
Auto-closing connection to: localhost:5432
Query result from localhost:5432
```

### 3. Auto-Destroy Secret

```austral
import LinearAutoDestroy (
    AutoSecret,
    makeAutoSecret,
    useAutoSecret
);

-- Secret é automaticamente apagado da memória após uso
let secret: AutoSecret := makeAutoSecret("my-password");
let result: String := useAutoSecret(secret);
-- secret foi destruído e memória foi zerada!

printLn(result);
```

**Output:**
```
Auto-wiping secret from memory...
Secret processed
```

### 4. Auto-Destroy Lock

```austral
import LinearAutoDestroy (
    AutoLock,
    acquireAutoLock,
    withAutoLock
);

-- Lock é automaticamente liberado após ação
let lock: AutoLock := acquireAutoLock("critical-section");
let result: String := withAutoLock(lock, fn(_: Unit): String is
    printLn("Executing critical section...");
    return "Done";
end);
-- lock foi destruído!

printLn(result);
```

**Output:**
```
Acquiring lock on: critical-section
Lock acquired on: critical-section
Executing critical section...
Auto-releasing lock on: critical-section
Done
```

### 5. Auto-Destroy Transaction

```austral
import LinearAutoDestroy (
    AutoTransaction,
    beginAutoTransaction,
    commitAuto
);

-- Transaction é automaticamente rolled back se não commitada
let tx: AutoTransaction := beginAutoTransaction();
-- Se não chamar commitAuto, rollback automático!
let success: Bool := commitAuto(tx);
-- tx foi destruído!
```

**Output:**
```
Beginning transaction...
Committing transaction...
```

## 🔧 API Reference

### Generic Auto-Destroy

```austral
-- Wrapper genérico para qualquer recurso
generic [T: Type, R: Linear]
record AutoDestroy is
    resource: R;
    extractor: Function[R, T];
    destructor: Function[R, Unit];
end;

-- Criar wrapper
generic [T: Type, R: Linear]
function makeAutoDestroy(
    resource: R,
    extractor: Function[R, T],
    destructor: Function[R, Unit]
): AutoDestroy[T, R];

-- Extrair valor (destrói recurso automaticamente)
generic [T: Type, R: Linear]
function extract(wrapper: AutoDestroy[T, R]): T;
```

### Scoped Resource Pattern

```austral
-- Executar ação com recurso e cleanup automático
generic [T: Type, R: Linear]
function withResource(
    resource: R,
    action: Function[R, T],
    cleanup: Function[R, Unit]
): T;
```

### Specific Types

```austral
-- Auto-destroy file
record AutoFile: Linear;
function openAutoFile(path: String): AutoFile;
function readAutoFile(file: AutoFile): String;

-- Auto-destroy connection
record AutoConnection: Linear;
function connectAuto(host: String): AutoConnection;
function queryAuto(conn: AutoConnection, sql: String): String;

-- Auto-destroy secret
record AutoSecret: Linear;
function makeAutoSecret(content: String): AutoSecret;
function useAutoSecret(secret: AutoSecret): String;

-- Auto-destroy lock
record AutoLock: Linear;
function acquireAutoLock(resource: String): AutoLock;
function withAutoLock(lock: AutoLock, action: Function[Unit, T]): T;

-- Auto-destroy transaction
record AutoTransaction: Linear;
function beginAutoTransaction(): AutoTransaction;
function commitAuto(tx: AutoTransaction): Bool;
```

## 💡 Use Cases

### 1. Database Operations

```austral
function getUser(userId: String): User is
    -- Conexão automaticamente fechada
    let conn: AutoConnection := connectAuto("db.example.com");
    let sql: String := concat("SELECT * FROM users WHERE id = '", userId, "'");
    let result: String := queryAuto(conn, sql);
    return parseUser(result);
end;
```

### 2. File Processing

```austral
function processConfig(path: String): Config is
    -- Arquivo automaticamente fechado
    let file: AutoFile := openAutoFile(path);
    let content: String := readAutoFile(file);
    return parseConfig(content);
end;
```

### 3. Secure Password Handling

```austral
function hashPassword(password: String): String is
    -- Password automaticamente apagado da memória
    let secret: AutoSecret := makeAutoSecret(password);
    let hash: String := useAutoSecret(secret);
    return hash;
end;
```

### 4. Critical Sections

```austral
function updateCounter(): Nat64 is
    let lock: AutoLock := acquireAutoLock("counter");
    return withAutoLock(lock, fn(_: Unit): Nat64 is
        let current: Nat64 := readCounter();
        let updated: Nat64 := current + 1;
        writeCounter(updated);
        return updated;
    end);
end;
```

### 5. Transactional Operations

```austral
function transferMoney(from: Account, to: Account, amount: Money): Bool is
    let tx: AutoTransaction := beginAutoTransaction();
    
    -- Operações
    debit(from, amount);
    credit(to, amount);
    
    -- Commit (ou rollback automático se falhar)
    return commitAuto(tx);
end;
```

## 🎯 Padrão RAII Explicado

### O Que é RAII?

**RAII (Resource Acquisition Is Initialization)** é um padrão onde:
1. Recurso é adquirido na criação do objeto
2. Recurso é liberado na destruição do objeto
3. Lifetime do objeto = lifetime do recurso

### Como Funciona no Austral?

```austral
-- 1. Recurso é adquirido
let file: AutoFile := openAutoFile("data.txt");
-- Arquivo aberto aqui

-- 2. Recurso é usado
let content: String := readAutoFile(file);
-- Durante a leitura...

-- 3. Recurso é automaticamente destruído
-- Arquivo fechado automaticamente quando readAutoFile retorna!
```

### Vantagens

✅ **Impossível esquecer cleanup** - Compilador garante  
✅ **Exception-safe** - Cleanup mesmo com erros  
✅ **Código mais limpo** - Menos boilerplate  
✅ **Zero overhead** - Sem custo em runtime  
✅ **Type-safe** - Garantias em compile-time  

## 🔒 Garantias de Segurança

### 1. Não Pode Usar Após Destruição

```austral
let file: AutoFile := openAutoFile("data.txt");
let content: String := readAutoFile(file);
-- let content2: String := readAutoFile(file); -- ERRO! file já foi destruído
```

### 2. Não Pode Duplicar Recursos

```austral
let file: AutoFile := openAutoFile("data.txt");
-- let file2: AutoFile := file; -- ERRO! AutoFile é Linear
```

### 3. Deve Consumir Recurso

```austral
function badExample(): Unit is
    let file: AutoFile := openAutoFile("data.txt");
    -- ERRO! file não foi consumido
    return nil;
end;

function goodExample(): Unit is
    let file: AutoFile := openAutoFile("data.txt");
    let _ := readAutoFile(file); -- OK! file consumido
    return nil;
end;
```

## 🆚 Comparação com Outros Padrões

### Manual Cleanup
```austral
-- ❌ Pode esquecer de fechar
let file := open("data.txt");
let content := read(file);
close(file); -- Pode esquecer!
```

### Try-Finally (outras linguagens)
```python
# ❌ Boilerplate, pode ter bugs
try:
    file = open("data.txt")
    content = file.read()
finally:
    file.close()
```

### Auto-Destroy (Austral)
```austral
-- ✅ Impossível esquecer, garantido pelo compilador
let file := openAutoFile("data.txt");
let content := readAutoFile(file);
-- Fechamento automático!
```

## 🏗️ Criando Seus Próprios Auto-Destroy Types

### Exemplo: Auto-Destroy HTTP Request

```austral
record AutoRequest: Linear is
    requestId: Int32;
    url: String;
end;

function makeAutoRequest(url: String): AutoRequest is
    printLn(concat("Creating request to: ", url));
    return AutoRequest(
        requestId => 123,
        url => url
    );
end;

function sendAutoRequest(req: AutoRequest): String is
    printLn(concat("Sending request to: ", req.url));
    
    -- Send request
    let response: String := "Response from " ++ req.url;
    
    -- Auto-cleanup
    printLn(concat("Auto-cleaning request to: ", req.url));
    
    return response;
end;

-- Uso
let req: AutoRequest := makeAutoRequest("https://api.example.com");
let response: String := sendAutoRequest(req);
-- req foi destruído automaticamente!
```

## 📊 Performance

- **Zero overhead** - Cleanup inline, sem alocações extras
- **Compile-time** - Todas as garantias em compile-time
- **No GC** - Sem garbage collector, determinístico
- **Predictable** - Cleanup exatamente quando esperado

## 🧪 Testing

```bash
make test
```

**Output:**
```
Testing Linear AutoDestroy Module...

=== Testing Auto-Destroy File ===
Opening file: data.txt
Reading file: data.txt
Auto-closing file: data.txt
Content: File content from: data.txt
✓ File automatically closed after read

=== Testing Auto-Destroy Connection ===
Connecting to: localhost:5432
Executing query: SELECT * FROM users
Auto-closing connection to: localhost:5432
Result: Query result from localhost:5432
✓ Connection automatically closed after query

=== Testing Auto-Destroy Secret ===
Auto-wiping secret from memory...
Result: Secret processed
✓ Secret automatically wiped from memory

=== Testing Auto-Destroy Lock ===
Acquiring lock on: critical-section
Lock acquired on: critical-section
Executing critical section...
Auto-releasing lock on: critical-section
Result: Critical section completed
✓ Lock automatically released after action

=== Testing Auto-Destroy Transaction ===
Beginning transaction...
Committing transaction...
✓ Transaction committed successfully

All tests passed!
```

## 📦 Installation

```bash
aurora install linear-autodestroy
```

## 🔗 Related Packages

- `file-handle` - Manual file handling
- `crypto-hash` - Secure hashing with auto-cleanup
- `secret-handle` - Secret management
- `db-transaction` - Database transactions

## 📝 License

Apache-2.0

---

**🎉 Cleanup automático, zero bugs, 100% seguro!**
