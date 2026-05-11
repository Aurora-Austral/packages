# 📁 File Handle

Safe file manipulation with linear types for Aurora Austral.

## Features

- **Linear File Handles** - Files must be explicitly closed
- **No File Descriptor Leaks** - Compiler enforces proper cleanup
- **Type-Safe Operations** - Read/write operations are type-checked
- **Error Handling** - Result types for safe error handling

## Why Linear Types?

```austral
-- FileHandle is Linear - must be closed
let handle: FileHandle := openFile("data.txt", ReadOnly);
let (content, handle2) := readAll(handle);
-- handle is consumed, must use handle2
let _ := closeFile(handle2);
-- handle2 is now consumed, cannot be used again
-- This prevents file descriptor leaks!
```

## Usage

### Reading Files

```austral
import FileHandle (
    openFile,
    readAll,
    closeFile,
    ReadOnly
);

let result := openFile("config.txt", ReadOnly);
case result of
    when Success(handle) then
        let (content, handle2) := readAll(handle);
        case content of
            when Success(text) then
                printLn(text);
            when Error(msg) then
                printLn(msg);
        end case;
        let _ := closeFile(handle2);
    when Error(msg) then
        printLn(concat("Failed to open: ", msg));
end case;
```

### Writing Files

```austral
import FileHandle (
    openFile,
    writeString,
    closeFile,
    WriteOnly
);

let result := openFile("output.txt", WriteOnly);
case result of
    when Success(handle) then
        let (writeResult, handle2) := writeString(handle, "Hello, World!");
        let _ := closeFile(handle2);
    when Error(msg) then
        printLn(msg);
end case;
```

### Appending to Files

```austral
import FileHandle (
    openFile,
    appendString,
    closeFile,
    Append
);

let result := openFile("log.txt", Append);
case result of
    when Success(handle) then
        let (_, handle2) := appendString(handle, "Log entry\n");
        let _ := closeFile(handle2);
    when Error(msg) then
        printLn(msg);
end case;
```

## API Reference

### Types

```austral
-- Linear type - must be closed
record FileHandle: Linear is
    fd: Int32;
    path: String;
    mode: FileMode;
end;

-- File modes
union FileMode: Free is
    case ReadOnly;
    case WriteOnly;
    case ReadWrite;
    case Append;
end;

-- Result type
union FileResult[T: Type]: Free is
    case Success(value: T);
    case Error(message: String);
end;
```

### Functions

```austral
-- Open file
function openFile(path: String, mode: FileMode): FileResult[FileHandle];

-- Read operations (return handle for chaining)
function readAll(handle: FileHandle): (FileResult[String], FileHandle);
function readBytes(handle: FileHandle, count: Nat64): (FileResult[String], FileHandle);

-- Write operations (return handle for chaining)
function writeString(handle: FileHandle, content: String): (FileResult[Unit], FileHandle);
function appendString(handle: FileHandle, content: String): (FileResult[Unit], FileHandle);

-- Close file (consumes handle)
function closeFile(handle: FileHandle): FileResult[Unit];

-- Utilities
function fileSize(handle: FileHandle): (FileResult[Nat64], FileHandle);
function fileExists(path: String): Bool;
function deleteFile(path: String): FileResult[Unit];
```

## Safety Guarantees

### 1. No File Descriptor Leaks

```austral
-- This won't compile - handle not closed!
function badExample(): Unit is
    let result := openFile("test.txt", ReadOnly);
    case result of
        when Success(handle) then
            -- ERROR: handle not closed!
            return nil;
        when Error(_) then
            return nil;
    end case;
end;

-- This compiles - handle properly closed
function goodExample(): Unit is
    let result := openFile("test.txt", ReadOnly);
    case result of
        when Success(handle) then
            let _ := closeFile(handle); -- OK!
            return nil;
        when Error(_) then
            return nil;
    end case;
end;
```

### 2. Handle Chaining

```austral
-- Operations return the handle for chaining
let result := openFile("data.txt", ReadWrite);
case result of
    when Success(handle) then
        let (_, h1) := writeString(handle, "Line 1\n");
        let (_, h2) := writeString(h1, "Line 2\n");
        let (_, h3) := writeString(h2, "Line 3\n");
        let _ := closeFile(h3);
    when Error(msg) then
        printLn(msg);
end case;
```

### 3. Type-Safe Modes

```austral
-- Can't write to read-only file
let result := openFile("data.txt", ReadOnly);
case result of
    when Success(handle) then
        -- This would be a type error in a full implementation
        let (_, h) := writeString(handle, "data"); -- Should fail!
        let _ := closeFile(h);
    when Error(_) then
        return nil;
end case;
```

## Use Cases

### Configuration Files

```austral
function loadConfig(path: String): FileResult[Config] is
    let result := openFile(path, ReadOnly);
    case result of
        when Success(handle) then
            let (content, h) := readAll(handle);
            let _ := closeFile(h);
            case content of
                when Success(text) then
                    return Success(parseConfig(text));
                when Error(msg) then
                    return Error(msg);
            end case;
        when Error(msg) then
            return Error(msg);
    end case;
end;
```

### Logging

```austral
function log(message: String): Unit is
    let result := openFile("app.log", Append);
    case result of
        when Success(handle) then
            let timestamp := getCurrentTime();
            let entry := concat(timestamp, ": ", message, "\n");
            let (_, h) := appendString(handle, entry);
            let _ := closeFile(h);
        when Error(_) then
            -- Fallback to console
            printLn(message);
    end case;
    return nil;
end;
```

### Data Processing

```austral
function processFile(inputPath: String, outputPath: String): FileResult[Unit] is
    -- Open input
    let inResult := openFile(inputPath, ReadOnly);
    case inResult of
        when Success(inHandle) then
            -- Read data
            let (data, inHandle2) := readAll(inHandle);
            let _ := closeFile(inHandle2);
            
            case data of
                when Success(content) then
                    -- Process data
                    let processed := processData(content);
                    
                    -- Open output
                    let outResult := openFile(outputPath, WriteOnly);
                    case outResult of
                        when Success(outHandle) then
                            let (_, outHandle2) := writeString(outHandle, processed);
                            let _ := closeFile(outHandle2);
                            return Success(nil);
                        when Error(msg) then
                            return Error(msg);
                    end case;
                    
                when Error(msg) then
                    return Error(msg);
            end case;
            
        when Error(msg) then
            return Error(msg);
    end case;
end;
```

## Building

```bash
make compile  # Build library
make test     # Run tests
make clean    # Clean build artifacts
```

## Installation

```bash
aurora install file-handle
```

## License

Apache-2.0

## Related Packages

- `async-runtime` - Async file I/O
- `crypto-hash` - File hashing
- `json-parser` - Parse JSON files
