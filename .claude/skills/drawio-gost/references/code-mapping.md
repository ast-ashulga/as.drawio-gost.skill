# Code Construct to GOST Symbol — Draw.io Mapping Rules

Use this reference when analyzing source code to select the correct ГОСТ 19.701-90 symbol and draw.io style for each construct.

---

## Control Flow Constructs

### if / else if / else

```
Code:    if (condition) { ... } else { ... }
Symbol:  Decision diamond (rhombus)
Exits:   Exactly 2 — "Да" (true branch) and "Нет" (false branch)
```

- `else if` chains → chain of Decision diamonds (one per condition)
- NEVER create a diamond with 3+ exits

### switch / case / match

Decompose into a chain of Decision diamonds — one per case value:
```
Decision {x == 1?} --Да--> Case 1 handler
                   --Нет--> Decision {x == 2?} --Да--> Case 2 handler
                                                --Нет--> Default handler
```

### for loop

**Standard `for` with numeric range** — represent as a **Hexagon (Preparation / Подготовка)**:

```
[i = 0, N-1, 1]   ← Hexagon (var = start, end, step)
    |Да                |Нет
    ↓ (loop body)       → (continue after loop)
[body]
    ↓
    → back to Hexagon  ← back-edge (arrow MANDATORY)
```

The hexagon encodes all loop parameters in one block. Two exits:
- **"Да"** — enter loop body (condition still holds)
- **"Нет"** — exit loop (condition exhausted)

**Layout pattern:**
- Hexagon in main flow column, "Да" exits downward into body
- Back-edge routes via the RIGHT side back to the hexagon
- "Нет" exit routes to the LEFT side, then down to the next block after the loop

**Fallback:** If the `for` loop has a complex condition that does not fit `var = start, end, step` (e.g., pointer iteration, multiple conditions, no counter), use the Decision diamond decomposition instead:
```
[init]   ← Process
    ↓
{cond?}  ← Decision diamond
  |Да      |Нет
  ↓         → exit
[body + increment]
  → back to {cond?}
```

### while loop

Same as for-loop but without separate initialization/increment blocks:
```
{condition?}      ← Decision diamond at top
  |Да      |Нет
  ↓         → exit
[body]
  ↓
  → back to {condition?}
```

### do-while loop

Body comes FIRST, condition diamond at the bottom:
```
[body]            ← Process
  ↓
{condition?}      ← Decision diamond
  |Да      |Нет
  → back     → exit (continue)
  to [body]
```
Back-edge labeled "Да" goes upward to body.

### for-range / iterator

Treat like a standard for loop. Use **Hexagon** when the iterator has a clear start/end/step (e.g., `for (int i = 0; i < n; i++)`). Use Decision diamond for pointer/iterator-based loops without a numeric counter.

---

## Function / Method Constructs

### Function / method call
```
Symbol:  Predefined Process (rectangle with double vertical lines)
Style:   shape=mxgraph.flowchart.predefined_process
Text:    functionName(args)
```

Use for EVERY call to a named function, method, or procedure defined elsewhere. Each called function should have its own diagram page.

### Recursive call
Same as function call — Predefined Process. Add a comment annotation "рекурсия" if needed for clarity.

### Lambda / anonymous function
- Inline expression → Process rectangle
- Named/assigned → Predefined Process

---

## I/O Constructs

### Read from stdin / user input
```
Symbol:  Card (Карточка)
Style:   shape=card;size=18;
Text:    "Ввод x, y" or "scanf(...)"
```

### Print / write to stdout
```
Symbol:  Document (Документ)
Style:   shape=mxgraph.flowchart.document;
Text:    "Вывод result" or "printf(...)"
```

### Read from / write to file or database
```
Symbol:  Direct Access Storage (cylinder) for the data store
         + Process rectangle for the operation
```

---

## Error Handling

### try / catch / finally
```
[Risky operation]     ← Process
       ↓
{Error?}              ← Decision
  |Да        |Нет
  ↓           ↓
[Handle]    [Continue]
```

### throw / raise
```
{Error condition?}    ← Decision
  |Да        |Нет
  ↓           ↓
(Error)     [Continue]    ← Terminator for thrown exception
```

---

## Entry / Exit Points

| Code Construct | GOST Symbol | draw.io | Text |
|---|---|---|---|
| Function entry / `main()` | Terminator | `rounded=1;arcSize=50;` | "Начало" or function signature |
| `return value` | Terminator | `rounded=1;arcSize=50;` | "Возврат" or return value |
| `return` (void) | Terminator | `rounded=1;arcSize=50;` | "Конец" |
| `exit(code)` | Terminator | `rounded=1;arcSize=50;` | "Выход" |
| Multiple returns | One Terminator per path | — | Each labeled differently |

---

## Variable Operations

| Code Construct | GOST Symbol | Text Example |
|---|---|---|
| Variable assignment | Process | "sum = a + b" |
| Memory allocation | Process | "Выделение памяти для arr[N]" |
| Memory deallocation | Process | "Освобождение памяти" |
| Pointer operation | Process | "*ptr = value" |
| Array indexing | Process | "arr[i] = val" |
| Struct/field access | Process | "obj.field = val" |

---

## Naming Conventions for Symbol Text

| Symbol Type | Convention | Examples |
|---|---|---|
| Terminator | Noun or short phrase (Russian) | "Начало", "Конец", "Возврат" |
| Process | Assignment or imperative verb + object | "sum = a + b", "i = i + 1" |
| Decision | Condition ending with `?` | "i < N?", "x > 0?", "key найден?" |
| Predefined Process | Function signature | "sortRow(row, m)", "printMatrix(E, N, M)" |
| Card (input) | "Ввод" + entities | "Ввод N, M", "scanf(...)" |
| Document (output) | "Вывод" + entities | "Вывод result", "printf(...)" |
| Preparation (Hexagon) | Loop params: `var = start, end, step` | "i = 0, N-1, 1" |

---

## Diagram Scope Guidelines

| Code Scope | Diagram Structure |
|---|---|
| Single function | Full body on ONE diagram page; called functions = Predefined Process |
| Multiple functions | Each function gets its own `<diagram>` page in the .drawio file |
| Entire program | `main()` on first page; each helper function on subsequent pages |
| Class with methods | Each significant method gets its own page |

**Multi-page convention:** Page name = function name (e.g., `name="main()"`, `name="sortRow()"`).
