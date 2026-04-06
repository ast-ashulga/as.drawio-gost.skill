# ГОСТ 19.701-90 / ISO 5807:1985 — Draw.io Symbol Reference

## Standards Overview

**ГОСТ 19.701-90** is the Russian interstate standard for program flowcharts, a direct adoption of **ISO 5807:1985**. Both define identical symbols, sizing rules, and layout conventions. This reference provides the exact draw.io XML styles for each symbol.

---

## Dimension System

All symbol dimensions are based on two parameters from ГОСТ 19.003-80:

- **a** = symbol height
- **b** = symbol width
- **Ratio:** `b = 2a` (permitted alternative for clarity; standard is `b = 1.5a`)

**All symbols in one diagram MUST use the same `a` value.**

### Recommended Pixel Sizes

| Profile | a (px) | b (px) | Use Case |
|---------|--------|--------|----------|
| Compact | 40 | 80 | Dense diagrams, many blocks |
| Standard | 50 | 100 | Default for most lab work |
| Large | 60 | 120 | Simple algorithms, presentations |

Default: **a=50, b=100**.

---

## Base Style (applied to ALL symbols)

Every symbol MUST include these style properties:

```
fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
```

- White fill only — NO decorative colors
- Black stroke, 1.5px width
- Times New Roman font (ГОСТ 2.304 compatible)
- Black font color

---

## Symbol Catalogue

### Terminators and Connectors

#### Terminator (Терминатор / Пуск-Останов)
- **Shape:** Rounded rectangle / stadium (semicircular ends)
- **Dimensions:** width = `b`, height = `a/2` (half normal height)
- **draw.io style:**
  ```
  rounded=1;arcSize=50;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Text:** "Начало" (Start), "Конец" (End), function signature, or "Возврат" (Return)

#### Connector (Соединитель)
- **Shape:** Small circle
- **Dimensions:** width = height = `a/2` (or `a*0.4` for compact)
- **draw.io style:**
  ```
  ellipse;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Rule:** Connectors work in PAIRS with IDENTICAL identifiers (e.g., "1"—"1", "A"—"A")

---

### Process Symbols

#### Process (Процесс)
- **Shape:** Plain rectangle
- **Dimensions:** width = `b`, height = `a`
- **draw.io style:**
  ```
  fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Use:** Any calculation, assignment, operation, state change
- **Text:** Operation description (e.g., "sum = a + b", "i = i + 1")

#### Predefined Process (Предопределенный процесс / Подпрограмма)
- **Shape:** Rectangle with two internal vertical lines near left and right edges
- **Dimensions:** width = `b`, height = `a`
- **draw.io style:**
  ```
  shape=mxgraph.flowchart.predefined_process;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Use:** Call to a named function/subroutine defined elsewhere
- **Text:** Function name with arguments (e.g., "sortRow(row, m)")

#### Decision (Решение)
- **Shape:** Diamond / rhombus
- **Dimensions:** width = `b`, height = `a` (diamond inscribed in bounding box)
- **draw.io style:**
  ```
  rhombus;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Rule:** ONE entry (top), exactly TWO exits labeled "Да" and "Нет"
- **Text:** Condition ending with "?" (e.g., "i < N?", "x > 0?")

#### Preparation / Modification (Подготовка)
- **Shape:** Hexagon (elongated horizontally)
- **Dimensions:** width = `b`, height = `a`
- **draw.io style:**
  ```
  shape=hexagon;perimeter=hexagonPerimeter2;size=0.2;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Use:** Loop counter initialization / for-loop header
- **Text:** Loop parameters "i = 1, n, 1" (variable = start, end, step)
- **Connection rule:** Input (incoming flow and back-edge) MUST connect to the **top center** of the hexagon (`entryX=0.5;entryY=0`). NO input connections to left or right edges. Exits ("Да" from bottom, "Нет" from left) are separate outputs.

#### Manual Operation (Ручная операция)
- **Shape:** Inverted trapezoid (wider at top)
- **Dimensions:** width = `b`, height = `a`
- **draw.io style:**
  ```
  shape=mxgraph.flowchart.manual_operation;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Use:** Process performed by a human without automation

#### Parallel Actions (Параллельные действия)
- **Shape:** Two horizontal parallel bars
- **draw.io style:** Use a thin rectangle (width = `b`, height = 4px) as a synchronization bar

---

### Data Symbols

#### Data / I/O (Данные / Ввод-вывод)
- **Shape:** Parallelogram
- **Dimensions:** width = `b`, height = `a`
- **draw.io style:**
  ```
  shape=parallelogram;perimeter=parallelogramPerimeter;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Use:** Generic data flow or mixed I/O when a more specific symbol is not applicable. Prefer **Card** for keyboard input and **Document** for printed/screen output.

#### Card (Карточка / Ввод с клавиатуры)
- **Shape:** Rectangle with a cut upper-left corner
- **Dimensions:** width = `b`, height = `a`
- **draw.io style:**
  ```
  shape=card;size=18;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Use:** Keyboard / user input — `scanf`, `cin`, manual data entry
- **Text:** "Ввод N, M"

#### Document (Документ)
- **Shape:** Rectangle with wavy bottom edge
- **Dimensions:** width = `b`, height = `a`
- **draw.io style:**
  ```
  shape=mxgraph.flowchart.document;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Use:** Data presented in human-readable form (printout, report)

#### Manual Input (Ручной ввод)
- **Shape:** Quadrilateral with slanted top edge
- **Dimensions:** width = `b`, height = `a`
- **draw.io style:**
  ```
  shape=mxgraph.flowchart.manual_input;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Use:** Data entered manually (keyboard input)

#### Display (Дисплей)
- **Shape:** CRT/monitor shape
- **Dimensions:** width = `b`, height = `a`
- **draw.io style:**
  ```
  shape=mxgraph.flowchart.display;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Use:** Data displayed on screen

#### Direct Access Storage (Запоминающее устройство с прямым доступом)
- **Shape:** Cylinder
- **Dimensions:** width = `b`, height = `a`
- **draw.io style:**
  ```
  shape=cylinder3;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;size=0.15;
  ```
- **Use:** Database, disk file, random-access storage

#### Sequential Access Storage (Запоминающее устройство с последовательным доступом)
- **Shape:** Circle (tape reel)
- **draw.io style:**
  ```
  ellipse;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```

#### Stored Data (Запоминаемые данные)
- **Shape:** Rectangle with curved left side
- **draw.io style:**
  ```
  shape=mxgraph.flowchart.stored_data;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```

---

### Loop Boundary Symbols

#### Loop Begin (Граница цикла — начало)
- **Shape:** Rectangle with chamfered upper corners (pentagon)
- **Dimensions:** width = `b`, height = `a*0.6`
- **draw.io style:**
  ```
  shape=mxgraph.flowchart.loop_limit;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Text:** Loop name + condition for pre-test loops (e.g., "while x > 0")

#### Loop End (Граница цикла — конец)
- **Shape:** Rectangle with chamfered lower corners (inverted pentagon)
- **Dimensions:** width = `b`, height = `a*0.6`
- **draw.io style:**
  ```
  shape=mxgraph.flowchart.loop_limit;flipV=1;fillColor=#FFFFFF;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;
  ```
- **Text:** Loop name + condition for post-test loops (e.g., "until x = 0")
- **Rule:** Both begin and end MUST carry the SAME identifier

---

### Line Symbols and Special Symbols

#### Flow Line (Линия)
- **draw.io edge style:**
  ```
  edgeStyle=orthogonalEdgeStyle;rounded=0;orthogonalLoop=1;jettySize=auto;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;
  ```
- Arrows optional in standard direction (top-to-bottom); MANDATORY for back-edges

#### Comment / Annotation (Комментарий)
- **Shape:** Open bracket connected by dashed line
- **draw.io style (bracket):**
  ```
  shape=mxgraph.flowchart.annotation_1;fillColor=none;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;whiteSpace=wrap;align=left;
  ```
- **Dashed connector style:**
  ```
  edgeStyle=orthogonalEdgeStyle;rounded=0;strokeColor=#000000;strokeWidth=1;dashed=1;endArrow=none;
  ```

---

## Edge Label Placement for Decision Blocks

Decision exit labels MUST be placed on the edges, not floating:

```xml
<mxCell id="e_yes" value="Да" style="edgeStyle=orthogonalEdgeStyle;rounded=0;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;" edge="1" source="decision_id" target="yes_target" parent="1">
    <mxGeometry relative="1" as="geometry"/>
</mxCell>
<mxCell id="e_no" value="Нет" style="edgeStyle=orthogonalEdgeStyle;rounded=0;strokeColor=#000000;strokeWidth=1.5;fontSize=11;fontFamily=Times New Roman;fontColor=#000000;" edge="1" source="decision_id" target="no_target" parent="1">
    <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

---

## Layout Rules (from ГОСТ 19.701-90 §4)

| Rule | Specification |
|------|---------------|
| Primary flow direction | Top-to-bottom |
| Secondary flow direction | Left-to-right |
| Line orientation | Horizontal and vertical ONLY (orthogonal) |
| Direction changes | 90-degree angles only |
| Arrows (standard direction) | Optional (but recommended for clarity) |
| Arrows (non-standard / back-edge) | MANDATORY |
| Entry to symbol | From top or left |
| Exit from symbol | From bottom or right |
| Min vertical spacing | >= `a` pixels between symbols |
| Text reading direction | Left-to-right, top-to-bottom |
| Identifier position | Upper-left of symbol |
| Symbol fill | White only — NO decorative colors |
