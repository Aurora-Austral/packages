# 🔒 Immutability in Austral - Complete Guide

## 🎯 O Que É Immutabilidade?

**Immutabilidade** significa que uma vez que um valor é criado, ele **não pode ser modificado**. Qualquer "modificação" cria um **novo valor**.

## 🆚 Mutable vs Immutable

### Mutable (Modificável)

```austral
-- Valor pode ser alterado
let counter: Int64 := 0;
counter := counter + 1; -- Modifica o valor existente
counter := counter * 2; -- Modifica novamente
```

**Problemas:**
- ❌ Difícil rastrear mudanças
- ❌ Race conditions em concorrência
- ❌ Bugs de "side effects"
- ❌ Difícil reasoning sobre código

### Immutable (Não Modificável)

```austral
-- Valor não pode ser alterado
let counter: Immutable[Int64] := makeImmutable(0);
-- counter := counter + 1; -- ERRO! Não pode modificar

-- Transformação cria NOVO valor
let counter2: Immutable[Int64] := mapImmutable(counter, fn(n): Int64 is n + 1 end);
```

**Vantagens:**
- ✅ Valor seguro e previsível
- ✅ Thread-safe por natureza
- ✅ Fácil reasoning sobre código
- ✅ Sem side effects

## 🧠 Como Funciona em Austral?

### 1. Record Wrapper

```austral
-- Wrapper genérico
generic [T: Type]
record Immutable is
    value: T;
end;

-- Criar imutável
let num: Immutable[Int64] := Immutable(value => 42);

-- Extrair valor
let value: Int64 := num.value;
```

### 2. Type Safety

```austral
-- Se você tentar modificar:
let num: Immutable[Int64] := makeImmutable(42);
-- num := makeImmutable(100); -- ERRO DE COMPILAÇÃO!

-- Porque Immutable é um record, não um valor primitivo
-- Você precisa extrair o valor primeiro
```

### 3. Transformação com Map

```austral
-- Transforma e cria NOVO imutável
let num: Immutable[Int64] := makeImmutable(10);
let doubled: Immutable[Int64] := mapImmutable(num, fn(n): Int64 is n * 2 end);

-- num e doubled são valores diferentes
-- num NÃO foi modificado!
```

## 🎨 Padrões de Uso

### Padrão 1: Create → Use

```austral
-- Criar imutável
let config: Immutable[Config] := makeImmutable(loadConfig());

-- Usar
let host: String := getImmutable(config).host;
let port: Int64 := getImmutable(config).port;
```

### Padrão 2: Create → Transform → Use

```austral
-- Criar
let num: Immutable[Int64] := makeImmutable(10);

-- Transformar
let doubled: Immutable[Int64] := mapImmutable(num, fn(n): Int64 is n * 2 end);
let plusFive: Immutable[Int64] := mapImmutable(doubled, fn(n): Int64 is n + 5 end);

-- Usar
let result: Int64 := getImmutable(plusFive); -- 25
```

### Padrão 3: Pipeline

```austral
-- Encadeamento de transformações
let result: Immutable[Int64] := mapImmutable(
    mapImmutable(
        makeImmutable(10),
        fn(n): Int64 is n * 2 end
    ),
    fn(n): Int64 is n + 5 end
);

-- 10 -> 20 -> 25
```

### Padrão 4: Scoped Operations

```austral
-- Executa ação com o valor
let result: Int64 := withImmutable(
    makeImmutable(10),
    fn(n): Int64 is
        return n * 2 + 5;
    end
);
```

## 🔒 Garantias de Segurança

### 1. Compile-Time Guarantee

```austral
-- O compilador garante que:
-- 1. Valor não pode ser modificado
-- 2. Valor não pode ser duplicado (se Linear)
-- 3. Valor deve ser consumido

let num: Immutable[Int64] := makeImmutable(42);

-- Erro: não pode reatribuir
-- num := makeImmutable(100);

-- Erro: não pode duplicar (se Linear)
-- let num2 := num;

-- OK: extrair valor
let value: Int64 := getImmutable(num);
```

### 2. Thread Safety

```austral
-- Dados imutáveis são seguros para concorrência
let sharedData: Immutable[Data] := makeImmutable(createData());

-- Múltiplas threads podem ler simultaneamente
-- Sem locks, sem race conditions!
```

### 3. No Side Effects

```austral
-- Funções puras com dados imutáveis
function process(data: Immutable[Data]): Immutable[Result] is
    -- data não pode ser modificado
    -- Result é um NOVO valor
    return transform(data);
end;

-- Sem side effects = código previsível
```

## 📊 Performance

### Zero Overhead

```austral
-- Immutable wrapper é "zero-cost abstraction"
-- Não há custo em runtime

let num: Immutable[Int64] := makeImmutable(42);
let value: Int64 := getImmutable(num);

-- Compilador otimiza wrapper away
-- Resultado é tão rápido quanto código mutable
```

### Memory Efficiency

```austral
-- Transformações podem compartilhar estrutura
-- Se usar estruturas de dados persistentes

-- Exemplo com árvore:
let tree1: Immutable[Tree] := makeImmutable(createTree());
let tree2: Immutable[Tree] := insert(tree1, key, value);

-- tree2 compartilha estrutura com tree1
-- Apenas nós modificados são copiados
```

## 🎯 Quando Usar?

### ✅ Use Para:

1. **Configuration**
   ```austral
   let config: Immutable[Config] := loadConfig();
   ```

2. **API Responses**
   ```austral
   let user: Immutable[User] := fetchUser("123");
   ```

3. **State Management**
   ```austral
   let state: Immutable[AppState] := updateState(currentState, action);
   ```

4. **Thread-Safe Data**
   ```austral
   let shared: Immutable[Data] := makeImmutable(createData());
   ```

5. **Functional Programming**
   ```austral
   let result := mapImmutable(mapImmutable(data, f), g);
   ```

### ⚠️ Não Use Para:

1. **High-frequency updates**
   ```austral
   -- Se você precisa atualizar milhares de vezes por segundo
   -- Considere estruturas de dados persistentes
   ```

2. **Large data with small changes**
   ```austral
   -- Se modifica 1 byte em 1GB de dados
   -- Considere copy-on-write
   ```

3. **Mutable resources**
   ```austral
   -- File handles, network connections
   -- Use linear-autodestroy para isso
   ```

## 🆚 Comparação com Outras Linguagens

| Linguagem | Immutabilidade | Performance |
|-----------|----------------|-------------|
| **Austral** | Compile-time | Zero overhead |
| Rust | Compile-time | Zero overhead |
| Haskell | Runtime | GC overhead |
| Java | Optional | GC overhead |
| Python | Optional | GC overhead |
| JavaScript | Optional | GC overhead |

## 🔧 Extensibilidade

### Criar Seu Próprio Tipo Imutável

```austral
-- 1. Defina seu tipo
record MyData is
    field1: Int64;
    field2: String;
end;

-- 2. Crie wrapper imutável
record ImmutableMyData: Free is
    data: MyData;
end;

-- 3. Funções de uso
function makeImmutableMyData(data: MyData): ImmutableMyData is
    return ImmutableMyData(data => data);
end;

function getImmutableMyData(data: ImmutableMyData): MyData is
    return data.data;
end;

function mapImmutableMyData(
    data: ImmutableMyData,
    transformer: Function[MyData, MyData]
): ImmutableMyData is
    let newData := transformer(data.data);
    return makeImmutableMyData(newData);
end;

-- 4. Uso
let myData := makeImmutableMyData(MyData(field1 => 42, field2 => "test"));
let transformed := mapImmutableMyData(myData, fn(d): MyData is
    return MyData(field1 => d.field1 * 2, field2 => d.field2);
end);
```

## 📚 Padrões Avançados

### 1. Persistent Data Structures

```austral
-- Árvore binária persistente
record TreeNode: Free is
    value: Int64;
    left: Option[Immutable[TreeNode]];
    right: Option[Immutable[TreeNode]];
end;

function insert(root: Immutable[TreeNode], value: Int64): Immutable[TreeNode] is
    -- Cria nova árvore compartilhando estrutura
    -- Apenas nós modificados são copiados
end;
```

### 2. Transactional Memory

```austral
-- Transações com dados imutáveis
function transaction(
    state: Immutable[AppState],
    operations: List[Operation]
): Immutable[AppState] is
    -- Cria nova versão do estado
    -- Sem locks, sem conflitos
end;
```

### 3. Event Sourcing

```austral
-- Histórico de eventos imutáveis
record Event: Free is
    timestamp: Int64;
    eventType: String;
    payload: Immutable[Data];
end;

function appendEvent(
    history: Immutable[List[Event]],
    event: Event
): Immutable[List[Event]] is
    -- Cria NOVA lista com evento adicionado
    -- History original não modificado
end;
```

## 🎓 Best Practices

1. **Use Immutable por padrão**
   ```austral
   -- Comece com imutável, só use mutable se necessário
   ```

2. **Transform, don't mutate**
   ```austral
   -- Sempre use mapImmutable para transformar
   ```

3. **Componha funções**
   ```austral
   -- Encadeie transformações
   ```

4. **Use withImmutable para scoped operations**
   ```austral
   -- Evite criar variáveis intermediárias
   ```

5. **Document immutable contracts**
   ```austral
   -- Documente que funções não modificam dados
   ```

## 📊 Exemplos Práticos

### Exemplo 1: User Profile

```austral
record UserProfile is
    id: String;
    name: String;
    email: String;
    age: Int64;
end;

function updateUserEmail(
    profile: Immutable[UserProfile],
    newEmail: String
): Immutable[UserProfile] is
    return mapImmutable(profile, fn(p): UserProfile is
        return UserProfile(
            id => p.id,
            name => p.name,
            email => newEmail,
            age => p.age
        );
    end);
end;

-- Uso
let profile := makeImmutable(UserProfile(...));
let updated := updateUserEmail(profile, "new@email.com");
```

### Exemplo 2: Counter

```austral
function increment(counter: Immutable[Int64]): Immutable[Int64] is
    return mapImmutable(counter, fn(n): Int64 is n + 1 end);
end;

let counter := makeImmutable(0);
counter := increment(counter); -- 1
counter := increment(counter); -- 2
counter := increment(counter); -- 3
```

### Exemplo 3: List Processing

```austral
function processList(
    list: Immutable[List[Int64]]
): Immutable[List[Int64]] is
    return mapImmutable(list, fn(nums): List[Int64] is
        return filter(map(nums, fn(n): Int64 is n * 2 end), fn(n): Bool is n > 10 end);
    end);
end;
```

## 🎯 Conclusão

**Immutabilidade em Austral** oferece:
- ✅ Segurança em compile-time
- ✅ Zero overhead em runtime
- ✅ Thread-safe por natureza
- ✅ Código mais previsível
- ✅ Fácil reasoning

**Use Immutable por padrão, só use mutable quando necessário!**

---

**📚 Veja também:**
- [README.md](README.md) - Documentação principal
- [linear-autodestroy](../linear-autodestroy/) - Auto-cleanup de recursos
