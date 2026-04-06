# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

A **Claude Code skill** that generates ГОСТ 19.701-90 / ISO 5807:1985 compliant algorithm flowcharts as native `.drawio` files. It is documentation-only — no build system, no dependencies, no tests.

The skill is loaded automatically by Claude Code from `.claude/skills/drawio-gost/SKILL.md` when a user invokes `/drawio-gost` or triggers its activation conditions (diagram/flowchart requests).

## Repository Structure

```
.claude/skills/drawio-gost/
├── SKILL.md              # Main skill — loaded by Claude as instructions
└── references/
    ├── gost-symbols.md   # draw.io XML styles for all 25+ GOST symbols
    └── code-mapping.md   # Maps code constructs (if/else, loops, etc.) to GOST symbols
```

## Skill Architecture

The skill is split into three layers that work together:

1. **SKILL.md** — Entry point. Defines activation triggers, the 4-step workflow (analyze → map → generate XML → export/open), platform-specific draw.io CLI detection (macOS, Linux, WSL2, Windows), and export procedures.

2. **gost-symbols.md** — Symbol library. Every GOST symbol with its exact draw.io XML `style=` string, canonical dimensions (`a=50px`, `b=100px`, with ratios per symbol type), and layout rules from ГОСТ 19.701-90 §4.

3. **code-mapping.md** — Code-to-symbol translator. Maps language constructs to GOST symbols: `if/else` → Decision diamond (2 exits: "Да"/"Нет"), `for`/`while` → back-edge pattern, function calls → Predefined Process, I/O → Data parallelogram, etc.

## Key Constraints (enforced by the skill)

- All symbols use **Times New Roman**, white fill, black 1.5px stroke — no decorative colors
- Decision blocks have **exactly 2 exits** ("Да"/"Нет"), never 3+
- Loop back-edges route RIGHT (iteration) and LEFT (exit) per GOST §4
- Multi-function diagrams use one `<diagram>` page per function
- Russian labels: "Начало", "Конец", "Ввод", "Вывод", etc.

## Editing This Skill

When modifying skill behavior, the layers are independent:
- Change **workflow or export logic** → edit `SKILL.md`
- Change **symbol styles or dimensions** → edit `gost-symbols.md`
- Change **code construct mappings** → edit `code-mapping.md`

The `.claude/settings.local.json` controls which domains the skill can fetch from (GOST standards sites) and which commands it can run (draw.io CLI, `open`).
