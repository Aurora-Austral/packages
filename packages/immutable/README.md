# 🔒 Immutable

Compile-time immutable data structures for Aurora Austral.

## 🎯 O Que É?

O módulo `immutable` fornece **estruturas de dados imutáveis** com garantias em compile-time. Uma vez criado, um valor imutável **não pode ser modificado** - qualquer tentativa de modificação resulta em erro de compilação.

## 🚀 Por Que Usar?

### ❌ Sem Immutabilidade
```austral
-- Dados podem ser modificados a qualquer momento
let counter: Int64 := 0;
counter := counter + 1; -- Pode ser modificado
counter := counter * 2; -- Pode ser modificado novamente
-- Difícil rastrear onde mudanças ocorrem
```

### ✅ Com Immutabilidade
```austral
-- Dados não podem ser modificados
let counter: Immutable[Int64] := makeImmutable(0);
-- let counter := counter + 1; -- ERRO! Não pode modificar
-- let counter := counter * 2; -- ERRO! Não pode modificar
-- Valor é seguro e previsível
```

## 📦 Features

- **🛡️ Immutabilidade Garantida** - Compilador previne modificações
- **⚡ Zero Overhead** - Sem custo em runtime
- **🎯 Type-Safe** - Garantias em compile-time
- **🔄 Transformação Segura** - `mapImmutable` cria novos valores
- **📦 Generic** - Funciona com qualquer tipo
- **🔒 Thread-Safe** - Imutável = seguro para concorrência

## 🎨 Uso Básico

### 1. Criar Immutável

```austral
import Immutable (
    makeImmutable,
    getImmutable
);

-- Criar valor imutável
let num: Immutable[Int64] := makeImmutable(42);
let value: Int64 := getImmutable(num);

printLn(intToString(value)); -- Output: 42
```

### 2. Transformar Immutável

```austral
import Immutable (
    mapImmutable
);

let num: Immutable[Int64] := makeImmutable(10);

-- Transforma e cria novo imutável
let doubled: Immutable[Int64] := mapImmutable(num, fn(n): Int64 is
    return n * 2;
end);

let value: Int64 := getImmutable(doubled);
printLn(intToString(value)); -- Output: 20

-- Original não mudou!
let original: Int64 := getImmutable(num);
printLn(intToString(original)); -- Output: 10
```

### 3. Usar com withImmutable

```austral
import Immutable (
    withImmutable
);

let num: Immutable[Int64] := makeImmutable(10);

-- Executa ação com o valor
let result: Int64 := withImmutable(num, fn(n): Int64 is
    return n * 2 + 5;
end);

printLn(intToString(result)); -- Output: 25
```

## 📊 Tipos Específicos

### ImmutableString

```austral
import Immutable (
    makeImmutableString,
    getImmutableString
);

let str: ImmutableString := makeImmutableString("Hello, World!");
let content: String := getImmutableString(str);

printLn(content); -- Output: Hello, World!
```

### ImmutableNumber

```austral
import Immutable (
    makeImmutableInt,
    makeImmutableNat,
    getImmutableNumber
);

let intNum: ImmutableNumber[Int64] := makeImmutableInt(-42);
let natNum: ImmutableNumber[Nat64] := makeImmutableNat(123);

let intVal: Int64 := getImmutableNumber(intNum);
let natVal: Nat64 := getImmutableNumber(natNum);
```

### ImmutableArray

```austral
import Immutable (
    makeImmutableArray,
    getImmutableArray
);

-- Criar array fixo
let arr: FixedArray[Int64, 3] := makeFixedArray[Int64, 3](0);
setFixedArray(&arr, 0, 1);
setFixedArray(&arr, 1, 2);
setFixedArray(&arr, 2, 3);

-- Wraper imutável
let immArr: ImmutableArray[Int64, 3] := makeImmutableArray(arr);
let retrieved: FixedArray[Int64, 3] := getImmutableArray(immArr);
```

### ImmutableOption

```austral
import Immutable (
    makeImmutableSome,
    makeImmutableNone,
    getImmutableOption
);

-- Com valor
let someVal: ImmutableOption[Int64] := makeImmutableSome(42);
let value: Int64 := getImmutableOption(someVal);

-- Sem valor
let noneVal: ImmutableOption[Int64] := makeImmutableNone[Int64]();
```

### ImmutableResult

```austral
import Immutable (
    makeImmutableSuccess,
    makeImmutableError,
    getImmutableResult
);

-- Sucesso
let success: ImmutableResult[Int64, String] := makeImmutableSuccess(42, "error");
let value: Int64 := getImmutableResult(success);

-- Erro
let error: ImmutableResult[Int64, String] := makeImmutableError(42, "Something went wrong");
```

## 🔧 API Reference

### Generic Immutable

```austral
-- Wrapper genérico
generic [T: Type]
record Immutable is
    value: T;
end;

-- Criar imutável
generic [T: Type]
function makeImmutable(value: T): Immutable[T];

-- Extrair valor
generic [T: Type]
function getImmutable(immutable: Immutable[T]): T;

-- Transformar (retorna novo imutável)
generic [T: Type, U: Type]
function mapImmutable(
    immutable: Immutable[T],
    transformer: Function[T, U]
): Immutable[U];

-- Scoped operations
generic [T: Type, U: Type]
function withImmutable(
    immutable: Immutable[T],
    action: Function[T, U]
): U;
```

### ImmutableString

```austral
record ImmutableString: Free;
function makeImmutableString(content: String): ImmutableString;
function getImmutableString(str: ImmutableString): String;
```

### ImmutableNumber

```austral
generic [N: Type]
record ImmutableNumber is
    value: N;
end;

function makeImmutableInt(value: Int64): ImmutableNumber[Int64];
function makeImmutableNat(value: Nat64): ImmutableNumber[Nat64];
function getImmutableNumber(num: ImmutableNumber[N]): N;
```

### ImmutableArray

```austral
generic [T: Type, N: Nat]
record ImmutableArray is
    data: FixedArray[T, N];
end;

function makeImmutableArray[T: Type, N: Nat](values: FixedArray[T, N]): ImmutableArray[T, N];
function getImmutableArray[T: Type, N: Nat](arr: ImmutableArray[T, N]): FixedArray[T, N];
function immutableArrayLength[T: Type, N: Nat](arr: ImmutableArray[T, N]): Nat;
```

### ImmutableOption

```austral
generic [T: Type]
union ImmutableOption: Free is
    case Some(value: T);
    case None;
end;

function makeImmutableSome[T: Type](value: T): ImmutableOption[T];
function makeImmutableNone[T: Type]: ImmutableOption[T];
function getImmutableOption[T: Type](opt: ImmutableOption[T]): T;
```

### ImmutableResult

```austral
generic [T: Type, E: Type]
union ImmutableResult: Free is
    case Success(value: T);
    case Error(error: E);
end;

function makeImmutableSuccess[T: Type, E: Type](value: T): ImmutableResult[T, E];
function makeImmutableError[T: Type, E: Type](error: E): ImmutableResult[T, E];
function getImmutableResult[T: Type, E: Type](result: ImmutableResult[T, E]): T;
```

## 💡 Use Cases

### 1. Configuration

```austral
function loadConfig(): Immutable[Config] is
    let config := readConfigFile();
    return makeImmutable(config);
end;

-- Config não pode ser modificado
let config: Immutable[Config] := loadConfig();
-- config.someField := "new" -- ERRO!
```

### 2. API Responses

```austral
function fetchUser(userId: String): Immutable[User] is
    let user := apiCall(userId);
    return makeImmutable(user);
end;

-- Response seguro contra modificações
let user: Immutable[User] := fetchUser("123");
```

### 3. State Management

```austral
-- Cada atualização cria novo estado imutável
function updateCounter(state: Immutable[Int64]): Immutable[Int64] is
    return mapImmutable(state, fn(n): Int64 is
        return n + 1;
    end);
end;

let state: Immutable[Int64] := makeImmutable(0);
state := updateCounter(state); -- Novo estado
state := updateCounter(state); -- Outro novo estado
```

### 4. Thread-Safe Data

```austral
-- Dados imutáveis são seguros para concorrência
let sharedData: Immutable[Data] := makeImmutable(createData());

-- Múltiplas threads podem ler simultaneamente
-- Sem locks, sem race conditions!
```

### 5. Functional Programming

```austral
-- Pipeline de transformações
let result: Immutable[Int64] := mapImmutable(
    mapImmutable(
        makeImmutable(10),
        fn(n): Int64 is n * 2 end
    ),
    fn(n): Int64 is n + 5 end
);

-- 10 -> 20 -> 25
```

## 🎯 Garantias de Segurança

### 1. Não Pode Modificar

```austral
let num: Immutable[Int64] := makeImmutable(42);
-- num := makeImmutable(100); -- ERRO! num já é imutável
```

### 2. Não Pode Duplicar (se Linear)

```austral
-- Se Immutable for Linear:
let num: Immutable[Int64] := makeImmutable(42);
-- let num2 := num; -- ERRO! Linear type
```

### 3. Transformação Cria Novo Valor

```austral
let num: Immutable[Int64] := makeImmutable(10);
let doubled: Immutable[Int64] := mapImmutable(num, fn(n): Int64 is n * 2 end);

-- num e doubled são valores diferentes
-- num não foi modificado!
```

## 🆚 Comparação com Outros Padrões

| Abordagem | Segurança | Performance | Complexidade |
|-----------|-----------|-------------|--------------|
| Mutable | ❌ Pode ser modificado | ✅ Ótima | ✅ Simples |
| Clone | ⚠️ Cópia cara | ❌ Lento | ✅ Simples |
| **Immutable** | ✅ Garantido | ✅ Zero overhead | ⚠️ Transformação cria novo valor |

## 📊 Performance

- **Zero overhead** - Sem alocações extras
- **Compile-time** - Todas as garantias em compile-time
- **No GC** - Sem garbage collector
- **Predictable** - Comportamento previsível

## 🧪 Testing

```bash
make test
```

**Output:**
```
Testing Immutable Module...

=== Testing Basic Immutable ===
✓ Immutable value created and retrieved
✓ withImmutable works correctly

=== Testing Immutable String ===
✓ Immutable string created and retrieved

=== Testing Immutable Numbers ===
✓ Immutable Int64 works
✓ Immutable Nat64 works

=== Testing Immutable Array ===
✓ Immutable array works

=== Testing Immutable Option ===
✓ Immutable Some works
✓ Immutable None works

=== Testing Immutable Result ===
✓ Immutable Success works
✓ Immutable Error works

=== Testing Map Immutable ===
✓ mapImmutable works correctly
✓ Original immutable unchanged

All tests passed!
```

## 📦 Installation

```bash
aurora install immutable
```

## 🔗 Related Packages

- `linear-autodestroy` - Auto-cleanup de recursos
- `crypto-hash` - Hashing com segurança
- `file-handle` - File handling

## 📝 License

Apache-2.0

---

**🔒 Dados imutáveis = Código mais seguro e previsível!**
