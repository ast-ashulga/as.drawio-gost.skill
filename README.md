# as.drawio-gost.skill

A Claude Code skill that generates **ГОСТ 19.701-90 / ISO 5807:1985** compliant algorithm flowcharts as native `.drawio` files.

## What it does

When you ask Claude to create a diagram, flowchart, or block diagram (блок-схема), this skill:

1. Reads and analyzes the source code or description
2. Maps each construct to the correct GOST symbol (decisions, loops, I/O, subroutines, etc.)
3. Generates native `.drawio` XML with strictly compliant styles, dimensions, and layout
4. Optionally exports to PNG, SVG, or PDF with embedded diagram XML (editable in draw.io)

## Activation

Trigger automatically by asking Claude to:
- "Create a flowchart for this function"
- "Draw a block diagram"
- "Generate a `.drawio` diagram"
- Mention draw.io, flowchart, algorithm diagram, etc.

Or invoke directly with `/drawio-gost`.

## GOST compliance

- All symbols use **Times New Roman**, white fill, black 1.5px stroke
- Decision blocks have **exactly 2 exits**: "Да" / "Нет"
- Loop back-edges route RIGHT (iteration) and LEFT (exit) per ГОСТ 19.701-90 §4
- Russian labels: "Начало", "Конец", "Ввод", "Вывод", etc.
- Multi-function diagrams use one page per function

## Supported output formats

| Format | Embedded XML | Notes |
|--------|-------------|-------|
| `.drawio` | — | Native, editable in draw.io |
| `.drawio.png` | Yes | Viewable everywhere, re-editable |
| `.drawio.svg` | Yes | Scalable, re-editable |
| `.drawio.pdf` | Yes | Printable, re-editable |

## Repository structure

```
.claude/skills/drawio-gost/
├── SKILL.md                    # Skill entry point — workflow, CLI detection, export
└── references/
    ├── gost-symbols.md         # draw.io XML styles for all 25+ GOST symbols
    └── code-mapping.md         # Maps code constructs to GOST symbols
```

## Requirements

- Claude Code
- draw.io desktop app (for PNG/SVG/PDF export; `.drawio` files work without it)
