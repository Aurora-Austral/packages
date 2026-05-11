# 📦 Aurora Austral Packages Roadmap

Packages que exploram as capacidades únicas do Austral (linear types, capabilities, segurança de memória).

## ✅ Packages Implementados

### 1. 🔐 **crypto-hash** - Hashing Seguro
**Status:** ✅ Completo  
**Prioridade:** Alta  
**Capacidades Austral:** Linear types para dados sensíveis

**Features:**
- SHA-256 e SHA-512
- HMAC-SHA256
- Comparação constant-time (previne timing attacks)
- `SecretData` como linear type (não pode vazar)

**Uso:**
```austral
let password: SecretData := makeSecretData("my-password");
let hash: Hash256 := sha256(password);
-- password é consumido, não pode ser usado novamente!
```

---

### 2. 📁 **file-handle** - Manipulação Segura de Arquivos
**Status:** ✅ Completo  
**Prioridade:** Alta  
**Capacidades Austral:** Linear types para file descriptors

**Features:**
- File handles como linear types
- Garante fechamento de arquivos
- Previne file descriptor leaks
- Operações type-safe

**Uso:**
```austral
let result := openFile("data.txt", ReadOnly);
case result of
    when Success(handle) then
        let (content, h) := readAll(handle);
        let _ := closeFile(h); -- Obrigatório!
    when Error(msg) then
        printLn(msg);
end case;
```

---

### 3. 🔑 **dpop-token** (Existente)
**Status:** ✅ Completo  
**Prioridade:** Alta  
**Capacidades Austral:** Linear types para tokens

---

### 4. 🔐 **secret-handle** (Existente)
**Status:** ✅ Completo  
**Prioridade:** Alta  
**Capacidades Austral:** Linear types para secrets

---

### 5. 👤 **session-handle** (Existente)
**Status:** ✅ Completo  
**Prioridade:** Alta  
**Capacidades Austral:** Linear types para sessões

---

### 6. 💾 **db-transaction** (Existente)
**Status:** ✅ Completo  
**Prioridade:** Alta  
**Capacidades Austral:** Linear types para transações

---

### 7. 💰 **linear-balance** (Existente)
**Status:** ✅ Completo  
**Prioridade:** Média  
**Capacidades Austral:** Linear types para saldos financeiros

---

### 8. 📊 **linear-event** (Existente)
**Status:** ✅ Completo  
**Prioridade:** Média  
**Capacidades Austral:** Linear types para eventos

---

## 🔨 Packages Prioritários (Próximos)

### 9. 🔐 **crypto-aead** - Criptografia Autenticada
**Status:** 🔨 Planejado  
**Prioridade:** Alta  
**Capacidades Austral:** Linear types para chaves

**Features:**
- AES-GCM
- ChaCha20-Poly1305
- Chaves como linear types (não podem ser duplicadas)
- Authenticated encryption

**Uso:**
```austral
let key: CryptoKey := generateKey();
let (ciphertext, key2) := encrypt(key, plaintext);
let (plaintext2, key3) := decrypt(key2, ciphertext);
let _ := destroyKey(key3);
```

---

### 10. 🎲 **secure-random** - Números Aleatórios Seguros
**Status:** 🔨 Planejado  
**Prioridade:** Alta  
**Capacidades Austral:** Capabilities para /dev/urandom

**Features:**
- Interface para /dev/urandom
- Geração de bytes aleatórios
- Geração de números em ranges
- Capability-based access

**Uso:**
```austral
capability RandomAccess is
    function randomBytes(count: Nat64): Buffer;
end;

function generateToken(rng: RandomAccess): String is
    let bytes := rng.randomBytes(32);
    return bytesToHex(bytes);
end;
```

---

### 11. 🌐 **http-client** - Cliente HTTP
**Status:** 🔨 Planejado  
**Prioridade:** Alta  
**Capacidades Austral:** Linear types para conexões

**Features:**
- Conexões HTTP/HTTPS
- Linear types garantem fechamento
- Suporte a timeouts
- Headers type-safe

**Uso:**
```austral
let conn: HttpConnection := connect("https://api.example.com");
let (response, conn2) := get(conn, "/users/123");
let _ := closeConnection(conn2);
```

---

### 12. 🔄 **async-runtime** - Runtime Assíncrono
**Status:** 🔨 Planejado  
**Prioridade:** Alta  
**Capacidades Austral:** Linear types para futures

**Features:**
- Futures/Promises com linear types
- Garante que futures sejam awaited
- Event loop
- Async I/O

**Uso:**
```austral
let future: Future[String] := asyncReadFile("data.txt");
let result: String := await(future);
-- future é consumido, não pode ser awaited novamente
```

---

### 13. 📝 **json-parser** - Parser JSON
**Status:** 🔨 Planejado  
**Prioridade:** Alta  
**Capacidades Austral:** Type-safe parsing

**Features:**
- Parsing seguro
- Validação de schema
- Zero-copy quando possível
- Error handling robusto

**Uso:**
```austral
let json: String := "{\"name\":\"John\",\"age\":30}";
let result: JsonResult[User] := parseJson[User](json);
case result of
    when Success(user) then
        printLn(user.name);
    when Error(msg) then
        printLn(msg);
end case;
```

---

### 14. 🔗 **channel** - Canais de Comunicação
**Status:** 🔨 Planejado  
**Prioridade:** Alta  
**Capacidades Austral:** Linear types para ownership transfer

**Features:**
- MPSC (Multi-Producer Single-Consumer)
- Ownership transfer entre threads
- Type-safe message passing
- Bounded/unbounded channels

**Uso:**
```austral
let (sender, receiver): (Sender[String], Receiver[String]) := makeChannel();

-- Thread 1
let sender2 := send(sender, "Hello");

-- Thread 2
let (msg, receiver2) := receive(receiver);
printLn(msg); -- "Hello"
```

---

## 📊 Packages Importantes (Médio Prazo)

### 15. 🗄️ **sql-connection** - Pool de Conexões SQL
**Status:** 📋 Planejado  
**Prioridade:** Média

**Features:**
- Pool de conexões
- Conexões como linear types
- Suporte a PostgreSQL, SQLite
- Prepared statements

---

### 16. 🔐 **mutex** - Locks com Linear Types
**Status:** 📋 Planejado  
**Prioridade:** Média

**Features:**
- Mutex guards como linear types
- Garante unlock após lock
- Previne deadlocks
- Thread-safe data structures

---

### 17. 📦 **vector** - Array Dinâmico
**Status:** 📋 Planejado  
**Prioridade:** Média

**Features:**
- Crescimento automático
- Ownership claro
- Operações type-safe
- Base para outras coleções

---

### 18. 🗺️ **hashmap** - Hash Table
**Status:** 📋 Planejado  
**Prioridade:** Média

**Features:**
- Inserção/busca O(1)
- Ownership de chaves e valores
- Iteração segura
- Essencial para caches

---

### 19. 🔗 **url-parser** - Parser de URLs
**Status:** 📋 Planejado  
**Prioridade:** Média

**Features:**
- Parsing e validação
- Extração de componentes
- Query string parsing
- Essencial para HTTP

---

### 20. ✅ **validator** - Validação de Dados
**Status:** 📋 Planejado  
**Prioridade:** Média

**Features:**
- Email, CPF, CNPJ, etc.
- Regras customizáveis
- Previne injeção
- Validação composável

---

## 🎯 Packages Avançados (Longo Prazo)

### 21. 🌐 **http-server** - Servidor HTTP
**Status:** 💡 Futuro  
**Prioridade:** Baixa

---

### 22. 🔌 **websocket** - WebSocket
**Status:** 💡 Futuro  
**Prioridade:** Baixa

---

### 23. 🗄️ **kv-store** - Key-Value Store
**Status:** 💡 Futuro  
**Prioridade:** Baixa

---

### 24. 📅 **datetime** - Manipulação de Datas
**Status:** 💡 Futuro  
**Prioridade:** Baixa

---

### 25. 🆔 **uuid** - Geração de UUIDs
**Status:** 💡 Futuro  
**Prioridade:** Baixa

---

## 🎨 Packages que Exploram Capacidades Únicas

### **capability-system** - Sistema de Capabilities
**Conceito:** Sistema de permissões em compile-time

```austral
capability DatabaseAccess is
    function query(sql: String): Result;
end;

-- Função requer capability
function getUser(id: UserId, db: DatabaseAccess): User is
    return db.query("SELECT * FROM users WHERE id = ?");
end;
```

---

### **resource-pool** - Pool Genérico de Recursos
**Conceito:** Pool de recursos lineares

```austral
generic [T: Linear]
record ResourcePool is
    available: Queue[T];
    in_use: Set[ResourceId];
end;

-- Pool de conexões, file handles, etc.
```

---

### **linear-state-machine** - State Machines com Linear Types
**Conceito:** Estados como linear types

```austral
union ConnectionState: Linear is
    case Disconnected;
    case Connecting;
    case Connected;
    case Closed;
end;

-- Garante transições válidas em compile-time
function connect(state: Disconnected): Connecting is
    -- ...
end;
```

---

## 📈 Estatísticas

- **Total de Packages:** 25+
- **Implementados:** 8 (32%)
- **Alta Prioridade:** 6 (24%)
- **Média Prioridade:** 7 (28%)
- **Baixa Prioridade:** 4 (16%)

---

## 🎯 Roadmap de Implementação

### **Q1 2026** (Fundação)
- ✅ crypto-hash
- ✅ file-handle
- 🔨 crypto-aead
- 🔨 secure-random

### **Q2 2026** (Networking)
- 🔨 http-client
- 🔨 json-parser
- 🔨 url-parser

### **Q3 2026** (Async & Concorrência)
- 🔨 async-runtime
- 🔨 channel
- 🔨 mutex

### **Q4 2026** (Banco de Dados)
- 🔨 sql-connection
- 🔨 kv-store
- 🔨 vector
- 🔨 hashmap

### **2027** (Avançado)
- 🔨 http-server
- 🔨 websocket
- 🔨 validator
- 🔨 datetime
- 🔨 uuid

---

## 🚀 Como Contribuir

1. Escolha um package da lista
2. Implemente seguindo o padrão dos existentes
3. Adicione testes completos
4. Documente com exemplos
5. Faça PR para o vault

---

## 📚 Recursos

- [Austral Language](https://austral-lang.org/)
- [Linear Types Tutorial](https://austral-lang.org/tutorial/linear-types)
- [Capabilities Tutorial](https://austral-lang.org/tutorial/capabilities)

---

**Última atualização:** 2026-05-10
