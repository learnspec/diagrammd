# DiagramMD — Format Specification v0.1

> Part of the **LearnSpec** suite  
> Status: Draft — May 10, 2026

---

## Core Principle

DiagramMD serves a **dual role** in the LearnSpec suite:

**Syntax specification** — the canonical reference for all diagram block types usable across the suite. Other specs (LearnMD, QuizMD, FlashMD) delegate diagram documentation to DiagramMD. A diagram block valid in DiagramMD is valid everywhere in the suite.

**Standalone file format** — `.diagram.md` files may contain reusable diagrams, importable via `!import` from any content format.

DiagramMD is a **pure leaf format**: it imports and references no other LearnSpec format. How diagrams are rendered (server-side, client-side, hybrid) is left entirely to the player implementation.

| Principle | Description |
|---|---|
| **Markdown-first** | A `.diagram.md` file is valid Markdown readable in any editor |
| **File-native** | All diagrams live in files — no database required |
| **Graceful degradation** | In any standard reader, each block displays as readable plain-text code |
| **Player-agnostic** | The spec defines syntax, not render implementation |
| **AI-native** | Generatable and consumable by an LLM without specific tooling |

---

## Format Levels

| Level | Mechanism | Purpose |
|---|---|---|
| 0 | Diagram fenced blocks with minimal fields | Inline diagrams, readable everywhere |
| 1 | YAML frontmatter | File metadata (standalone only) |
| 2 | `id` attribute + enriched attributes | Reusable diagrams, captions, accessibility |

---

## Common Block Syntax

### Basic Structure

Every diagram block follows this syntax:

````
```{type} [id:slug] [caption:"..."] [width:value] [alt:"..."]
[diagram source]
```
````

### Common Attributes

These attributes are available on all block types:

| Attribute | Status | Description |
|---|---|---|
| `id` | Required in `.diagram.md` files | Unique slug within the file. Enables block identification. Optional when used inline. |
| `caption` | Optional | Caption displayed below the diagram. |
| `width` | Optional | Render width — CSS value (`80%`, `600px`, `100%`). Default: `100%`. |
| `alt` | Recommended | Accessibility alt text. Describes the visual content of the diagram. |

### Graceful Degradation

In any standard Markdown reader, a diagram block displays as a code block with the type name as the language identifier. The source content is readable as plain text — no understanding is lost.

---

## Diagram Types

### `mermaid` — Text-based Diagrams

[Mermaid.js](https://mermaid.js.org/) syntax for flowcharts, sequence diagrams, class diagrams, entity-relationship diagrams, Gantt charts, mindmaps, and timelines.

````markdown
```mermaid caption:"System architecture" width:80%
flowchart LR
    A[Client] --> B[API]
    B --> C[(Database)]
    B --> D[Cache]
```
````

**Supported Mermaid diagram types:**

| Keyword | Use case |
|---|---|
| `flowchart` / `graph` | Process flows, decision trees |
| `sequenceDiagram` | Component interactions |
| `classDiagram` | Object models, relationships |
| `erDiagram` | Database schemas |
| `gantt` | Schedules, milestones |
| `mindmap` | Topic hierarchies |
| `timeline` | Historical sequences |

**AI authoring recommendations:**
- Prefer `LR` orientation for sequential flows with more than 5 steps
- Keep node labels under 40 characters; use `\n` to break longer ones
- Avoid linear chains longer than 6 nodes; introduce subgraphs
- Limit vertical depth to 7 levels in `TD` orientation

---

### `tikz` — Scientific and Technical Diagrams

[TikZ/PGF](https://tikz.dev/) syntax for electrical circuits, physics diagrams, and geometry. Particularly suited to STEM content.

````markdown
```tikz caption:"Series RC circuit" width:60%
\begin{tikzpicture}
  \draw (0,0) to[R, l=$R$] (2,0)
              to[C, l=$C$] (4,0)
              to[battery, l=$V_0$] (4,2)
              -- (0,2) -- (0,0);
\end{tikzpicture}
```
````

---

### `graphviz` — Graphs and Networks

[DOT language](https://graphviz.org/doc/info/lang.html) syntax for directed and undirected graphs, trees, and dependency networks.

````markdown
```graphviz caption:"Module dependencies" alt:"Dependency graph between modules A, B, C and D"
digraph deps {
    rankdir=LR;
    A -> B;
    A -> C;
    B -> D;
    C -> D;
}
```
````

---

### `plantuml` — UML Diagrams

[PlantUML](https://plantuml.com/) syntax for activity, use case, component, deployment, and state diagrams.

````markdown
```plantuml caption:"Authentication process"
@startuml
actor User
User -> Server : login(email, password)
Server -> DB : verify(email, password)
DB --> Server : OK
Server --> User : JWT token
@enduml
```
````

---

### `blockdiag` — Block Diagrams

[blockdiag](http://blockdiag.com/) syntax for simple functional block diagrams.

````markdown
```blockdiag caption:"Processing pipeline"
blockdiag {
    Input -> Validation -> Processing -> Storage -> Output;
}
```
````

---

### `seqdiag` — Sequence Diagrams (Simplified Syntax)

[seqdiag](http://blockdiag.com/en/seqdiag/) syntax for sequence diagrams with a simpler syntax than Mermaid.

````markdown
```seqdiag caption:"Client-server exchange"
seqdiag {
    Client -> Server -> DB;
    DB --> Server --> Client;
}
```
````

---

### `chess` — Chessboard

Displays a chess position from FEN notation or a PGN excerpt.

````markdown
```chess caption:"Position after 1.e4 e5 2.Nf3" alt:"Chessboard with pawns on e4 and e5, knight on f3"
rnbqkbnr/pppp1ppp/8/4p3/4P3/5N2/PPPP1PPP/RNBQKB1R b KQkq - 1 2
```
````

**FEN (Forsyth-Edwards Notation)** — complete position on a single line.  
**PGN (Portable Game Notation)** — move sequence; the player displays the final position or enables move-by-move navigation.

---

### `abc` — Sheet Music

[ABC notation](https://abcnotation.com/) syntax for sheet music.

````markdown
```abc play cursor caption:"Main theme"
X:1
T:Main Theme
M:4/4
L:1/8
K:C
|: G2 AB c2 BA | G4 E4 :|
```
````

**Attributes specific to `abc`:**

| Attribute | Description |
|---|---|
| `play` | Adds audio playback controls |
| `cursor` | Highlights the current note during playback (requires `play`) |
| `colors` | Colours each note by pitch class (requires `play`) |

---

### `smiles` — Chemical Molecules

[SMILES](https://www.daylight.com/dayhtml/doc/theory/theory.smiles.html) (Simplified Molecular Input Line Entry System) notation for molecular structure representation.

````markdown
```smiles caption:"Caffeine" alt:"Molecular structure of caffeine"
CN1C=NC2=C1C(=O)N(C(=O)N2C)C
```
````

---

### `vega-lite` — Data Visualisations

[Vega-Lite](https://vega.github.io/vega-lite/) JSON specification for charts and data visualisations.

````markdown
```vega-lite caption:"Grade distribution" alt:"Bar chart of grades from 0 to 20"
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "mark": "bar",
  "data": {
    "values": [
      {"grade": "0-5", "count": 3},
      {"grade": "5-10", "count": 12},
      {"grade": "10-15", "count": 28},
      {"grade": "15-20", "count": 7}
    ]
  },
  "encoding": {
    "x": {"field": "grade", "type": "ordinal"},
    "y": {"field": "count", "type": "quantitative"}
  }
}
```
````

---

## Standalone `.diagram.md` File Format

### Structure

A `.diagram.md` file contains one or more diagram blocks, each identified by an `id`. The frontmatter is optional (Level 1).

````markdown
---
title: "Diagrams — System Architecture"
lang: en
spec_version: "0.1"
tags: [architecture, backend]
---

# Diagrams — System Architecture

```mermaid id:global-arch caption:"Architecture overview" width:90%
flowchart LR
    Client --> API
    API --> Cache
    API --> DB
```

```mermaid id:auth-flow caption:"Authentication flow"
sequenceDiagram
    Client->>API: POST /login
    API->>DB: verify credentials
    DB-->>API: OK
    API-->>Client: JWT token
```
````

### Importing into a Content Format

A `.diagram.md` file is imported via the standard `!import` directive. All blocks in the file are inserted at the directive's position.

```markdown
!import ./diagrams-architecture.diagram.md
```

```markdown
!import https://github.com/org/repo/blob/main/diagrams.diagram.md
```

**Granularity**: `!import` inserts the entire file. To insert a single diagram, use a dedicated file per diagram.

---

## Interoperability

| Mechanism | Support |
|---|---|
| Imported by LearnMD via `!import` | ✅ |
| Imported by QuizMD via `!import` | ✅ |
| Imported by FlashMD via `!import` | ✅ |
| `!import` of other formats | ❌ — leaf format |
| `!ref` of other formats | ❌ — leaf format |
| Imported by TrackMD | ❌ — TrackMD orchestrates content formats, not leaf formats |

---

## Validation

### Lenient Mode (default)

| Condition | Level |
|---|---|
| `lang` absent from frontmatter (standalone file) | Warning |
| Unrecognised block type | Warning |
| Duplicate `id` within the file | Error |
| `alt` absent | Warning |
| Empty diagram source | Error |
| `cursor` or `colors` on an `abc` block without `play` | Warning |

### Strict Mode (`--strict`)

All warnings are promoted to errors.

---

## Relationship with LearnMD

DiagramMD v0.1 absorbs and replaces the diagram block documentation currently present in LearnMD v0.3 (`mermaid`, `abc`). LearnMD v0.4 will delegate to DiagramMD as the canonical syntax reference for diagram blocks, while continuing to support these blocks inline without requiring `!import`.

---

*Released under the MIT License. Copyright © 2024-present LearnSpec Contributors*
