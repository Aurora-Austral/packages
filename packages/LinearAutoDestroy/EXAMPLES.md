# 🎨 Linear AutoDestroy - Exemplos Avançados

## 🔥 Exemplos Práticos

### 1. Pipeline de Processamento de Dados

```austral
-- Cada etapa automaticamente limpa recursos
function processDataPipeline(inputPath: String, outputPath: String): Bool is
    -- 1. Ler arquivo (auto-close)
    let inputFile: AutoFile := openAutoFile(inputPath);
    let rawData: String := readAutoFile(inputFile);
    -- inputFile destruído aqui!
    
    -- 2. Processar com secret (auto-wipe)
    let secret: AutoSecret := makeAutoSecret(rawData);
    let processed: String := useAutoSecret(secret);
    -- secret destruído e memória zerada!
    
    -- 3. Salvar resultado (auto-close)
    let outputFile: AutoFile := openAutoFile(outputPath);
    let _ := writeAutoFile(outputFile, processed);
    -- outputFile destruído aqui!
    
    return true;
end;
```

### 2. Transação Bancária Segura

```austral
function transferWithAutoCleanup(
    from: AccountId,
    to: AccountId,
    amount: Money
): Result[Unit, String] is
    -- Conexão auto-close
    let conn: AutoConnection := connectAuto("bank-db.internal");
    
    -- Transação auto-rollback se não commitada
    let tx: AutoTransaction := beginAutoTransaction();
    
    -- Lock auto-release
    let lock: AutoLock := acquireAutoLock("accounts");
    
    let result: Result[Unit, String] := withAutoLock(lock, fn(_: Unit): Result[Unit, String] is
        -- Operações dentro do lock
        let debitResult := queryAuto(conn, 
            concat("UPDATE accounts SET balance = balance - ", 
                   moneyToString(amount), 
                   " WHERE id = '", from, "'"));
        
        let creditResult := queryAuto(conn,
            concat("UPDATE accounts SET balance = balance + ",
                   moneyToString(amount),
                   " WHERE id = '", to, "'"));
        
        -- Commit se tudo OK
        if debitResult.success and creditResult.success then
            let _ := commitAuto(tx);
            return Success(nil);
        else
            -- Rollback automático (tx não commitado)
            return Error("Transfer failed");
        end if;
    end);
    
    -- Tudo foi automaticamente limpo:
    -- - lock liberado
    -- - tx commitado ou rolled back
    -- - conn fechada
    
    return result;
end;
```

### 3. API Request com Retry e Timeout

```austral
function apiRequestWithRetry(url: String, maxRetries: Nat64): Result[String, String] is
    let attempts: Nat64 := 0;
    
    while attempts < maxRetries do
        -- Cada tentativa cria nova conexão (auto-close)
        let conn: AutoConnection := connectAuto(url);
        
        -- Timeout automático
        let result: Result[String, String] := queryAuto(conn, "GET /api/data");
        -- conn destruída aqui!
        
        case result of
            when Success(data) then
                return Success(data);
            when Error(msg) then
                printLn(concat("Attempt ", intToString(attempts), " failed: ", msg));
                attempts := attempts + 1;
        end case;
    end while;
    
    return Error("Max retries exceeded");
end;
```

### 4. Processamento Paralelo com Locks

```austral
function parallelProcess(items: List[Item]): List[Result] is
    let results: List[Result] := emptyList();
    
    for item in items do
        -- Cada item processa com seu próprio lock (auto-release)
        let lockName: String := concat("item-", item.id);
        let lock: AutoLock := acquireAutoLock(lockName);
        
        let result: Result := withAutoLock(lock, fn(_: Unit): Result is
            return processItem(item);
        end);
        -- lock liberado automaticamente!
        
        results := append(results, result);
    end for;
    
    return results;
end;
```

### 5. Cache com Auto-Invalidation

```austral
record AutoCache: Linear is
    cacheId: Int32;
    ttl: Nat64;
end;

function withCache(key: String, ttl: Nat64, compute: Function[Unit, T]): T is
    let cache: AutoCache := acquireCache(key, ttl);
    
    -- Tenta buscar do cache
    let cached: Option[T] := getCached(cache, key);
    
    case cached of
        when Some(value) then
            -- Cache hit - retorna e destrói cache
            return value;
        when None then
            -- Cache miss - computa e salva
            let value: T := compute(nil);
            let _ := setCached(cache, key, value);
            return value;
    end case;
    -- cache destruído e invalidado automaticamente!
end;

-- Uso
let userData: User := withCache("user:123", 300, fn(_: Unit): User is
    return fetchUserFromDB("123");
end);
```

### 6. Logging com Buffer Auto-Flush

```austral
record AutoLogger: Linear is
    buffer: Buffer;
    maxSize: Nat64;
end;

function logWithAutoFlush(message: String): Unit is
    let logger: AutoLogger := getLogger();
    
    -- Adiciona ao buffer
    let logger2: AutoLogger := appendLog(logger, message);
    
    -- Se buffer cheio, flush automático
    if bufferFull(logger2) then
        let _ := flushLogger(logger2);
        -- logger2 destruído e buffer flushed!
    else
        let _ := releaseLogger(logger2);
    end if;
    
    return nil;
end;
```

### 7. WebSocket com Auto-Disconnect

```austral
record AutoWebSocket: Linear is
    socketId: Int32;
    url: String;
end;

function sendMessage(url: String, message: String): Bool is
    -- Conecta (auto-disconnect)
    let ws: AutoWebSocket := connectWebSocket(url);
    
    -- Envia mensagem
    let result: Bool := sendWS(ws, message);
    -- ws destruído e desconectado automaticamente!
    
    return result;
end;

-- Ou com múltiplas mensagens
function sendMessages(url: String, messages: List[String]): Bool is
    let ws: AutoWebSocket := connectWebSocket(url);
    
    for msg in messages do
        let ws2: AutoWebSocket := sendWS(ws, msg);
        ws := ws2; -- Atualiza handle
    end for;
    
    -- Desconecta automaticamente ao final
    let _ := closeWS(ws);
    return true;
end;
```

### 8. Temporary File com Auto-Delete

```austral
record AutoTempFile: Linear is
    path: String;
    fd: Int32;
end;

function withTempFile(action: Function[String, T]): T is
    -- Cria arquivo temporário (auto-delete)
    let temp: AutoTempFile := createTempFile();
    
    -- Executa ação com o path
    let result: T := action(temp.path);
    
    -- Arquivo deletado automaticamente
    let _ := deleteTempFile(temp);
    
    return result;
end;

-- Uso
let processed: Data := withTempFile(fn(tempPath: String): Data is
    -- Usa arquivo temporário
    writeToFile(tempPath, rawData);
    let processed := processFile(tempPath);
    return processed;
end);
-- Arquivo temporário deletado automaticamente!
```

### 9. Rate Limiter com Auto-Release

```austral
record AutoRateLimit: Linear is
    limitId: Int32;
    tokens: Nat64;
end;

function withRateLimit(action: Function[Unit, T]): Result[T, String] is
    -- Adquire token (auto-release)
    let limiter: AutoRateLimit := acquireToken();
    
    if hasTokens(limiter) then
        let result: T := action(nil);
        let _ := releaseToken(limiter);
        -- Token liberado automaticamente!
        return Success(result);
    else
        let _ := releaseToken(limiter);
        return Error("Rate limit exceeded");
    end if;
end;

-- Uso
let result: Result[Data, String] := withRateLimit(fn(_: Unit): Data is
    return callAPI();
end);
```

### 10. Distributed Lock com Auto-Release

```austral
record AutoDistributedLock: Linear is
    lockKey: String;
    leaseId: String;
    ttl: Nat64;
end;

function withDistributedLock(
    key: String,
    ttl: Nat64,
    action: Function[Unit, T]
): Result[T, String] is
    -- Adquire lock distribuído (auto-release)
    let lock: AutoDistributedLock := acquireDistributedLock(key, ttl);
    
    case lock of
        when Success(l) then
            -- Executa ação
            let result: T := action(nil);
            
            -- Lock liberado automaticamente
            let _ := releaseDistributedLock(l);
            
            return Success(result);
        when Error(msg) then
            return Error(msg);
    end case;
end;

-- Uso em cluster
let result: Result[Unit, String] := withDistributedLock(
    "global-counter",
    5000,
    fn(_: Unit): Unit is
        incrementGlobalCounter();
        return nil;
    end
);
```

## 🎯 Padrões Compostos

### Combinar Múltiplos Auto-Destroy

```austral
function complexOperation(): Result[Data, String] is
    -- 1. Lock (auto-release)
    let lock: AutoLock := acquireAutoLock("operation");
    
    return withAutoLock(lock, fn(_: Unit): Result[Data, String] is
        -- 2. Conexão (auto-close)
        let conn: AutoConnection := connectAuto("db.internal");
        
        -- 3. Transação (auto-rollback)
        let tx: AutoTransaction := beginAutoTransaction();
        
        -- 4. Secret (auto-wipe)
        let secret: AutoSecret := makeAutoSecret("sensitive-data");
        
        -- Operações
        let data1 := queryAuto(conn, "SELECT ...");
        let data2 := useAutoSecret(secret);
        let _ := commitAuto(tx);
        
        -- Tudo limpo automaticamente na ordem reversa:
        -- secret -> tx -> conn -> lock
        
        return Success(combineData(data1, data2));
    end);
end;
```

## 🚀 Performance Tips

### 1. Minimize Scope

```austral
-- ❌ Ruim - recurso vive muito tempo
function badExample(): Data is
    let conn: AutoConnection := connectAuto("db");
    let data1 := fetchData1();
    let data2 := fetchData2();
    let result := queryAuto(conn, "SELECT ...");
    return processData(result);
end;

-- ✅ Bom - recurso vive apenas o necessário
function goodExample(): Data is
    let data1 := fetchData1();
    let data2 := fetchData2();
    
    -- Conexão só quando necessário
    let conn: AutoConnection := connectAuto("db");
    let result := queryAuto(conn, "SELECT ...");
    -- conn destruída imediatamente!
    
    return processData(result);
end;
```

### 2. Batch Operations

```austral
-- ✅ Uma conexão para múltiplas queries
function batchQueries(queries: List[String]): List[Result] is
    let conn: AutoConnection := connectAuto("db");
    let results: List[Result] := emptyList();
    
    for query in queries do
        let (result, conn2) := queryAutoKeepAlive(conn, query);
        results := append(results, result);
        conn := conn2;
    end for;
    
    let _ := closeConnection(conn);
    return results;
end;
```

## 🎓 Best Practices

1. **Use auto-destroy para recursos escassos** (conexões, file descriptors, locks)
2. **Minimize o scope** dos recursos
3. **Combine com Result types** para error handling
4. **Documente o comportamento** de cleanup
5. **Teste edge cases** (erros, timeouts, etc.)

## 🔗 Veja Também

- [README.md](README.md) - Documentação principal
- [crypto-hash](../crypto-hash/) - Hashing com auto-cleanup
- [file-handle](../file-handle/) - File handling manual

---

**🎉 Cleanup automático = Código mais seguro e limpo!**
