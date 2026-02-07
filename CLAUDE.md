# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Market research and competitive analysis project for GemGem400 (잼잼400), a Korean digital healthcare startup specializing in AR-based pediatric rehabilitation. This is a pure documentation project — no build system, no application code.

## Repository Structure

All research content lives under `gemgem400/`:
- `README.md` — Executive summary and navigation
- `company-profile.md` — Company/product deep dive
- `market-analysis.md` — Market sizing, trends, SWOT analysis
- `competitors.md` — Competitive landscape and positioning
- `charts/diagrams.md` — Mermaid diagram collection (business model, tech stack, revenue scenarios, org structure)

## Conventions

- **Language**: All content is written in Korean with English terms where appropriate
- **Diagrams**: Mermaid syntax embedded in fenced code blocks. GitHub renders these natively but some chart types (e.g., `quadrantChart`) have limited Unicode/Korean support — test rendering on GitHub after changes
- **Tables**: Used extensively for structured comparisons (competitors, features, metrics)
- **Emoji**: Used as visual markers in headings and status indicators (✅, ⚠️, 🔴, 🟡, 🟢)

## Working with Mermaid Diagrams

The project uses these Mermaid diagram types: `graph`, `pie`, `quadrantChart`, `timeline`, `flowchart`.

**Known limitation**: `quadrantChart` axis labels and quadrant names must use ASCII-safe text or properly quoted strings. Korean text in axis/quadrant labels can cause lexical parsing errors on GitHub. Wrap non-ASCII labels in double quotes when needed.
