---
name: drawio-gost
description: Always use when user asks to create, generate, draw, or design a diagram, flowchart, architecture diagram, ER diagram, sequence diagram, class diagram, network diagram, mockup, wireframe, or UI sketch, or mentions draw.io, drawio, drawoi, .drawio files, or diagram export to PNG/SVG/PDF.
---

# Draw.io Diagram Skill

Generate draw.io diagrams as native `.drawio` files. Optionally export to PNG, SVG, or PDF with the diagram XML embedded (so the exported file remains editable in draw.io).

## How to create a diagram

1. **Generate draw.io XML** in mxGraphModel format for the requested diagram
2. **Write the XML** to a `.drawio` file in the current working directory using the Write tool
3. **If the user requested an export format** (png, svg, pdf), locate the draw.io CLI (see below), export with `--embed-diagram`, then delete the source `.drawio` file. If the CLI is not found, keep the `.drawio` file and tell the user they can install the draw.io desktop app to enable export, or open the `.drawio` file directly
4. **Open the result** — the exported file if exported, or the `.drawio` file otherwise. If the open command fails, print the file path so the user can open it manually

## Choosing the output format

Check the user's request for a format preference. Examples:

- `/drawio-gost create a flowchart` → `flowchart.drawio`
- `/drawio-gost png flowchart for login` → `login-flow.drawio.png`
- `/drawio-gost svg: ER diagram` → `er-diagram.drawio.svg`
- `/drawio-gost pdf architecture overview` → `architecture-overview.drawio.pdf`

If no format is mentioned, just write the `.drawio` file and open it in draw.io. The user can always ask to export later.

### Supported export formats

| Format | Embed XML | Notes |
|--------|-----------|-------|
| `png` | Yes (`-e`) | Viewable everywhere, editable in draw.io |
| `svg` | Yes (`-e`) | Scalable, editable in draw.io |
| `pdf` | Yes (`-e`) | Printable, editable in draw.io |
| `jpg` | No | Lossy, no embedded XML support |

PNG, SVG, and PDF all support `--embed-diagram` — the exported file contains the full diagram XML, so opening it in draw.io recovers the editable diagram.

## draw.io CLI

The draw.io desktop app includes a command-line interface for exporting.

### Locating the CLI

First, detect the environment, then locate the CLI accordingly:

#### WSL2 (Windows Subsystem for Linux)

WSL2 is detected when `/proc/version` contains `microsoft` or `WSL`:

```bash
grep -qi microsoft /proc/version 2>/dev/null && echo "WSL2"
```

On WSL2, use the Windows draw.io Desktop executable via `/mnt/c/...`:

```bash
DRAWIO_CMD=`/mnt/c/Program Files/draw.io/draw.io.exe`
```

The backtick quoting is required to handle the space in `Program Files` in bash.

If draw.io is installed in a non-default location, check common alternatives:

```bash
# Default install path
`/mnt/c/Program Files/draw.io/draw.io.exe`

# Per-user install (if the above does not exist)
`/mnt/c/Users/$WIN_USER/AppData/Local/Programs/draw.io/draw.io.exe`
```

#### macOS

```bash
/Applications/draw.io.app/Contents/MacOS/draw.io
```

#### Linux (native)

```bash
drawio   # typically on PATH via snap/apt/flatpak
```

#### Windows (native, non-WSL2)

```
"C:\Program Files\draw.io\draw.io.exe"
```

Use `which drawio` (or `where drawio` on Windows) to check if it's on PATH before falling back to the platform-specific path.

### Export command

```bash
drawio -x -f <format> -e -b 10 -o <output> <input.drawio>
```

**WSL2 example:**

```bash
`/mnt/c/Program Files/draw.io/draw.io.exe` -x -f png -e -b 10 -o diagram.drawio.png diagram.drawio
```

Key flags:
- `-x` / `--export`: export mode
- `-f` / `--format`: output format (png, svg, pdf, jpg)
- `-e` / `--embed-diagram`: embed diagram XML in the output (PNG, SVG, PDF only)
- `-o` / `--output`: output file path
- `-b` / `--border`: border width around diagram (default: 0)
- `-t` / `--transparent`: transparent background (PNG only)
- `-s` / `--scale`: scale the diagram size
- `--width` / `--height`: fit into specified dimensions (preserves aspect ratio)
- `-a` / `--all-pages`: export all pages (PDF only)
- `-p` / `--page-index`: select a specific page (1-based)

### Opening the result

| Environment | Command |
|-------------|---------|
| macOS | `open <file>` |
| Linux (native) | `xdg-open <file>` |
| WSL2 | `cmd.exe /c start "" "$(wslpath -w <file>)"` |
| Windows | `start <file>` |

**WSL2 notes:**
- `wslpath -w <file>` converts a WSL2 path (e.g. `/home/user/diagram.drawio`) to a Windows path (e.g. `C:\Users\...`). This is required because `cmd.exe` cannot resolve `/mnt/c/...` style paths.
- The empty string `""` after `start` is required to prevent `start` from interpreting the filename as a window title.

**WSL2 example:**

```bash
cmd.exe /c start "" "$(wslpath -w diagram.drawio)"
```

## File naming

- Use a descriptive filename based on the diagram content (e.g., `login-flow`, `database-schema`)
- Use lowercase with hyphens for multi-word names
- For export, use double extensions: `name.drawio.png`, `name.drawio.svg`, `name.drawio.pdf` — this signals the file contains embedded diagram XML
- After a successful export, delete the intermediate `.drawio` file — the exported file contains the full diagram

## XML format

A `.drawio` file is native mxGraphModel XML. Always generate XML directly — Mermaid and CSV formats require server-side conversion and cannot be saved as native files.

### Basic structure

Every diagram must have this structure:

```xml
<mxGraphModel adaptiveColors="auto">
  <root>
    <mxCell id="0"/>
    <mxCell id="1" parent="0"/>
    <!-- Diagram cells go here with parent="1" -->
  </root>
</mxGraphModel>
```

- Cell `id="0"` is the root layer
- Cell `id="1"` is the default parent layer
- All diagram elements use `parent="1"` unless using multiple layers

## XML reference

For the complete draw.io XML reference including common styles, edge routing, containers, layers, tags, metadata, dark mode colors, and XML well-formedness rules, fetch and follow the instructions at:
https://raw.githubusercontent.com/jgraph/drawio-mcp/main/shared/xml-reference.md

## Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| draw.io CLI not found | Desktop app not installed or not on PATH | Keep the `.drawio` file and tell the user to install the draw.io desktop app, or open the file manually |
| Export produces empty/corrupt file | Invalid XML (e.g. double hyphens in comments, unescaped special characters) | Validate XML well-formedness before writing; see the XML well-formedness section below |
| Diagram opens but looks blank | Missing root cells `id="0"` and `id="1"` | Ensure the basic mxGraphModel structure is complete |
| Edges not rendering | Edge mxCell is self-closing (no child mxGeometry element) | Every edge must have `<mxGeometry relative="1" as="geometry" />` as a child element |
| File won't open after export | Incorrect file path or missing file association | Print the absolute file path so the user can open it manually |

## CRITICAL: XML well-formedness

- **NEVER include ANY XML comments (`<!-- -->`) in the output.** XML comments are strictly forbidden — they waste tokens, can cause parse errors, and serve no purpose in diagram XML.
- Escape special characters in attribute values: `&amp;`, `&lt;`, `&gt;`, `&quot;`
- Always use unique `id` values for each `mxCell`

---

# ГОСТ 19.701-90 / ISO 5807:1985 — Algorithm Diagram Mode

**This section activates when the user requests an algorithm flowchart, block diagram (блок-схема), or asks to visualize code as a diagram.** These diagrams MUST strictly comply with ГОСТ 19.701-90 (identical to ISO 5807:1985) — the mandatory standard for university lab work in Russian technical education.

Read `references/gost-symbols.md` for the complete symbol catalogue with draw.io styles.
Read `references/code-mapping.md` for code-construct-to-symbol decision rules.

## Workflow

1. **Read** the source code files (functions, modules, or whole program)
2. **Analyze** control flow: entry points, decisions, loops, subroutine calls, I/O, exits
3. **Map** each construct to the correct GOST symbol (see references)
4. **Generate** draw.io XML with strictly compliant styles, dimensions, and layout
5. **Write** the `.drawio` file — one `<diagram>` page per function

## Dimension System

All symbol dimensions are based on parameters from ГОСТ 19.003-80:

| Parameter | Definition |
|-----------|-----------|
| **a** | Symbol height (base unit) |
| **b** | Symbol width = **2a** |

**All symbols in one diagram MUST use the same `a` value. No exceptions.**

### Default Pixel Sizes

Use **a = 50px, b = 100px** as the default. Override only if the user requests a different size or text doesn't fit.

If text is too long for standard `b` width, increase `b` for THAT symbol type to the next multiple of `a` (e.g., b=150 for wide text), but keep `a` constant. Alternatively, use a Comment annotation connected by a dashed line.

## Mandatory Base Style

**Every** symbol cell MUST include this base style:

```
fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
```

**Rules:**
- **NO decorative fill colors** — white fill only (`fillColor=#FFFFFF`)
- **NO colored strokes** — black only (`strokeColor=#000000`)
- **Consistent stroke width** — 1.5px (`strokeWidth=1.5`)
- **Times New Roman font** — ГОСТ 2.304 compatible (`fontFamily=Times New Roman`)
- **Font size 11** — readable at standard zoom

## Symbol Quick Reference

| GOST Symbol | draw.io Style (add base style) | Width | Height |
|---|---|---|---|
| **Terminator** (Начало/Конец) | `rounded=1;arcSize=50;` | b | **a/2** |
| **Process** (Процесс) | *(base style only — plain rectangle)* | b | a |
| **Decision** (Решение) | `rhombus;` | b | a |
| **Card** (Ввод / keyboard input) | `shape=card;size=18;` | b | a |
| **Document** (Вывод / output) | `shape=mxgraph.flowchart.document;` | b | a |
| **Predefined Process** (Подпрограмма) | `shape=mxgraph.flowchart.predefined_process;` | b | a |
| **Preparation** (Подготовка / for-loop) | `shape=hexagon;perimeter=hexagonPerimeter2;size=0.2;` | b | a |
| **Connector** (Соединитель) | `ellipse;` | a/2 | a/2 |
| **Loop Begin** (Граница цикла нач.) | `shape=mxgraph.flowchart.loop_limit;` | b | a×0.6 |
| **Loop End** (Граница цикла кон.) | `shape=mxgraph.flowchart.loop_limit;flipV=1;` | b | a×0.6 |
| **Data I/O** (generic) | `shape=parallelogram;perimeter=parallelogramPerimeter;` | b | a |
| **Comment** (Комментарий) | `shape=mxgraph.flowchart.annotation_1;fillColor=none;align=left;` | varies | varies |

## Edge Style

**All** edges MUST use this style:

```
edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;
```

- **Orthogonal only** — no curves, no diagonals
- **Arrows** on back-edges (going upward or right-to-left) are MANDATORY
- Standard-direction edges (downward) may omit arrows, but including them is recommended for clarity

## Decision Block Rules

Decision diamonds have **exactly 2 exits**, each MUST be labeled:

- **"Да"** (Yes) — the true/continue branch
- **"Нет"** (No) — the false/exit branch

Label placement via edge `value` attribute:
```xml
<mxCell id="e_yes" value="Да" style="..." edge="1" source="d1" target="body" parent="1">
    <mxGeometry relative="1" as="geometry"/>
</mxCell>
<mxCell id="e_no" value="Нет" style="..." edge="1" source="d1" target="after_loop" parent="1">
    <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

**NEVER:**
- Leave a Decision exit unlabeled
- Create a Decision with 3+ exits
- Use `switch/case` as a single diamond — decompose into a chain of diamonds

### Decision Exit Direction Convention

- **"Да" exits downward** (continues the main flow)
- **"Нет" exits to the left** (loop exit, branch termination) — routing via the LEFT side of the diagram
- Back-edges (loop iterations) route via the **RIGHT** side of the diagram

## Layout Rules

### Flow Direction
- **Primary:** top-to-bottom
- **Secondary:** left-to-right
- NEVER use bottom-to-top or right-to-left as primary direction

### Symbol Placement
- **Center-align** the main flow column horizontally
- **Vertical spacing** between symbols: `a` pixels minimum (50px at default size)
- Main flow column X-center: typically at x=240 (leaving room for left-side "Нет" routing)

### Back-edge Routing Pattern

#### for loop with Hexagon (Preparation)

Use a Hexagon for standard numeric `for` loops. The hexagon has two exits:

```
Main flow column (center)
    |
    v (entry: top center)
[i = 0, N-1, 1]        x=190..290 (centered at 240)  ← Hexagon
    |Да                  |Нет
    v                     → routes LEFT (x=60) then DOWN to after-loop block
[body]
    |
    +----> routes RIGHT (x=360) then UP to above Hexagon, then LEFT to top center
```

**Implementation with waypoints:**
```xml
<mxCell id="hex_loop" value="i = 0, N-1, 1" style="shape=hexagon;perimeter=hexagonPerimeter2;size=0.2;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;" vertex="1" parent="1">
    <mxGeometry x="190" y="(hex_y)" width="100" height="50" as="geometry"/>
</mxCell>

<mxCell id="back_edge" value="" style="edgeStyle=orthogonalEdgeStyle;rounded=0;strokeColor=#000000;strokeWidth=1.5;exitX=1;exitY=0.5;exitDx=0;exitDy=0;entryX=0.5;entryY=0;entryDx=0;entryDy=0;" edge="1" source="last_body_cell" target="hex_loop" parent="1">
    <mxGeometry relative="1" as="geometry">
        <Array as="points">
            <mxPoint x="360" y="(last_body_center_y)"/>
            <mxPoint x="360" y="(hex_y - 20)"/>
        </Array>
    </mxGeometry>
</mxCell>
```

For "Нет" exit routing left:
```xml
<mxCell id="no_exit" value="Нет" style="edgeStyle=orthogonalEdgeStyle;rounded=0;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;exitX=0;exitY=0.5;exitDx=0;exitDy=0;entryX=0;entryY=0.5;entryDx=0;entryDy=0;" edge="1" source="hex_loop" target="after_loop" parent="1">
    <mxGeometry relative="1" as="geometry">
        <Array as="points">
            <mxPoint x="60" y="(hex_center_y)"/>
            <mxPoint x="60" y="(after_loop_center_y)"/>
        </Array>
    </mxGeometry>
</mxCell>
```

#### while / complex for loop with Decision diamond

For `while` loops or `for` loops that don't fit `var = start, end, step`:

```
Main flow column (center)
    |
    v
{cond?} ----Нет----> routes LEFT (x=60) then DOWN to after-loop block
    |Да
    v
[body]
    |
    +----> routes RIGHT (x=360) then UP back to {cond?}
```

### Nested Loop Routing

For nested loops, stagger the routing columns:
- Inner loop back-edge: RIGHT at x=360
- Outer loop back-edge: RIGHT at x=400
- Inner loop "Нет" exit: LEFT at x=100
- Outer loop "Нет" exit: LEFT at x=60

This prevents line overlaps and maintains clean orthogonal routing.

## Multi-page Diagrams

Each function gets its own `<diagram>` page within a single `<mxfile>`:

```xml
<mxfile>
    <diagram id="page-main" name="main()">
        <mxGraphModel dx="800" dy="600" grid="1" gridSize="10" guides="1" tooltips="1" connect="1" arrows="1" fold="1" page="1" pageScale="1" pageWidth="850" pageHeight="1100" math="0" shadow="0">
            <root>
                <mxCell id="0"/>
                <mxCell id="1" parent="0"/>
                ...
            </root>
        </mxGraphModel>
    </diagram>
    <diagram id="page-sortRow" name="sortRow()">
        <mxGraphModel ...>
            <root>
                <mxCell id="0"/>
                <mxCell id="1" parent="0"/>
                ...
            </root>
        </mxGraphModel>
    </diagram>
</mxfile>
```

- Page name = function name with parentheses
- Each page has its own independent coordinate system
- Use Predefined Process symbols to reference functions on other pages

## Code-to-Symbol Mapping (Quick Reference)

| Code Construct | GOST Symbol | Notes |
|---|---|---|
| `if/else` | Decision diamond | 2 exits: "Да"/"Нет" |
| `switch/case` | Chain of Decision diamonds | One diamond per case value |
| `for` loop (numeric range) | Preparation Hexagon + body + back-edge | Hexagon text: "i = 0, N-1, 1" |
| `for` loop (complex condition) | Process (init) + Decision + body + Process (increment) + back-edge | Fallback when no clear start/end/step |
| `while` loop | Decision at top + body + back-edge | |
| `do-while` loop | body + Decision at bottom + back-edge labeled "Да" going up | |
| Function call | Predefined Process | Double-bordered rectangle |
| `scanf`/`cin`/input | Card (Карточка) | Text: "Ввод ..." |
| `printf`/`cout`/output | Document (Документ) | Text: "Вывод ..." |
| Function entry | Terminator | "Начало" or function signature |
| Function exit | Terminator | "Конец" or "Возврат" |
| Variable assignment | Process | "sum = a + b" |
| Memory alloc/free | Process | "Выделение/Освобождение памяти" |

For complete mapping rules, see `references/code-mapping.md`.

## Complete XML Template

This template shows the correct structure for a simple function with a loop. Use as a starting pattern:

```xml
<mxfile>
    <diagram id="page-example" name="example()">
        <mxGraphModel dx="800" dy="600" grid="1" gridSize="10" guides="1" tooltips="1" connect="1" arrows="1" fold="1" page="1" pageScale="1" pageWidth="850" pageHeight="1100" math="0" shadow="0">
            <root>
                <mxCell id="0"/>
                <mxCell id="1" parent="0"/>

                <mxCell id="t_start" value="Начало" style="rounded=1;arcSize=50;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;" vertex="1" parent="1">
                    <mxGeometry x="190" y="20" width="100" height="25" as="geometry"/>
                </mxCell>

                <mxCell id="io_input" value="Ввод N" style="shape=card;size=18;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;" vertex="1" parent="1">
                    <mxGeometry x="190" y="85" width="100" height="50" as="geometry"/>
                </mxCell>

                <mxCell id="hex_loop" value="i = 0, N-1, 1" style="shape=hexagon;perimeter=hexagonPerimeter2;size=0.2;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;" vertex="1" parent="1">
                    <mxGeometry x="190" y="175" width="100" height="50" as="geometry"/>
                </mxCell>

                <mxCell id="io_output" value="Вывод i" style="shape=mxgraph.flowchart.document;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;" vertex="1" parent="1">
                    <mxGeometry x="190" y="265" width="100" height="50" as="geometry"/>
                </mxCell>

                <mxCell id="t_end" value="Конец" style="rounded=1;arcSize=50;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;" vertex="1" parent="1">
                    <mxGeometry x="190" y="375" width="100" height="25" as="geometry"/>
                </mxCell>

                <mxCell id="e1" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;strokeColor=#000000;strokeWidth=1.5;" edge="1" source="t_start" target="io_input" parent="1">
                    <mxGeometry relative="1" as="geometry"/>
                </mxCell>

                <mxCell id="e2" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;strokeColor=#000000;strokeWidth=1.5;" edge="1" source="io_input" target="hex_loop" parent="1">
                    <mxGeometry relative="1" as="geometry"/>
                </mxCell>

                <mxCell id="e3" value="Да" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;" edge="1" source="hex_loop" target="io_output" parent="1">
                    <mxGeometry relative="1" as="geometry"/>
                </mxCell>

                <mxCell id="e4" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;strokeColor=#000000;strokeWidth=1.5;exitX=1;exitY=0.5;exitDx=0;exitDy=0;entryX=0.5;entryY=0;entryDx=0;entryDy=0;" edge="1" source="io_output" target="hex_loop" parent="1">
                    <mxGeometry relative="1" as="geometry">
                        <Array as="points">
                            <mxPoint x="360" y="290"/>
                            <mxPoint x="360" y="155"/>
                        </Array>
                    </mxGeometry>
                </mxCell>

                <mxCell id="e5" value="Нет" style="edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;exitX=0;exitY=0.5;exitDx=0;exitDy=0;entryX=0;entryY=0.5;entryDx=0;entryDy=0;" edge="1" source="hex_loop" target="t_end" parent="1">
                    <mxGeometry relative="1" as="geometry">
                        <Array as="points">
                            <mxPoint x="120" y="200"/>
                            <mxPoint x="120" y="388"/>
                        </Array>
                    </mxGeometry>
                </mxCell>
            </root>
        </mxGraphModel>
    </diagram>
</mxfile>
```

## What NOT to Do

- **NO fill colors** — never use green, blue, yellow fills; white only
- **NO varying symbol sizes** — keep `a` constant throughout the diagram
- **NO unlabeled Decision exits** — every branch must say "Да" or "Нет"
- **NO curved or diagonal edges** — orthogonal only
- **NO 3+ exit diamonds** — always exactly 2 exits per Decision
- **NO omitted START/END terminators** — every diagram needs both
- **NO invented logic** — only diagram what the code actually does
- **NO skipping function calls** — each called function gets a Predefined Process symbol
- **NO mixing fonts** — Times New Roman throughout
