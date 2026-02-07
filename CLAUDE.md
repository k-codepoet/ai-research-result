# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Market research and competitive analysis repository covering Korean companies and products. This is a pure documentation project — no build system, no application code. Each research topic lives in its own top-level directory.

## Repository Structure

Each research topic is a self-contained directory with a `README.md` as its entry point:

- `gemgem400/` — GemGem400 (잼잼400), AR-based pediatric rehabilitation startup
- `hyundai-motor/` — Hyundai Motor Company analysis
- `nexon/` — Nexon analysis (AX transformation, Palantir comparison)
- `maplestory-worlds/` — MapleStory Worlds UGC platform analysis
- `maplestory-worlds-strategy/` — MapleStory Worlds strategic deep-dive (leadership vision, gap analysis, recommendations)

**Standard directory pattern** (gemgem400, hyundai-motor):
```
<topic>/
├── README.md              # Executive summary and navigation
├── company-profile.md     # Company/product deep dive
├── market-analysis.md     # Market sizing, trends, SWOT
├── competitors.md         # Competitive landscape
└── charts/
    └── diagrams.md        # Mermaid chart collection
```

Some topics deviate — nexon adds `ax-transformation.md` and `palantir-comparison.md`; maplestory-worlds-strategy uses numbered files (`01-leadership-vision.md`, `02-current-state.md`, etc.).

## Conventions

- **Language**: All content is written in Korean with English terms where appropriate
- **Diagrams**: Mermaid syntax embedded in fenced code blocks. GitHub renders these natively but some chart types (e.g., `quadrantChart`) have limited Unicode/Korean support — test rendering on GitHub after changes
- **Tables**: Used extensively for structured comparisons (competitors, features, metrics)
- **Emoji**: Used as visual markers in headings and status indicators (✅, ⚠️, 🔴, 🟡, 🟢)
- **New topics**: Create a new top-level directory following the standard pattern above. Always include a `README.md` with an executive summary, analysis date, and table of contents linking to sub-documents

## Working with Mermaid Diagrams

The project uses these Mermaid diagram types: `graph`, `pie`, `quadrantChart`, `timeline`, `flowchart`.

**Known limitation**: `quadrantChart` axis labels and quadrant names must use ASCII-safe text or properly quoted strings. Korean text in axis/quadrant labels can cause lexical parsing errors on GitHub. Wrap non-ASCII labels in double quotes when needed.
