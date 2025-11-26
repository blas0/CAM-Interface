# Severance
> [!IMPORTANT] 
> [Notice](#Notice) | [Disclaimer](#Disclaimer) | [Credit](#Credit) | [Claude's Cognition](https://github.com/blas0/CAM-Interface/blob/main/INTRODUCTION.md)

> **The control center for CAM (Continuous Architectural Memory)**
>
> Severance is the version-controlled source of truth for your entire CAM infrastructure hooks, templates, settings, and core engine. Clone it. Customize it. Deploy it globally. Every project benefits.

---

## What is Severance?

Severance is **not just another project with CAM installed** it's the **development repository** that deploys to your global `~/.claude/` directory. When you modify CAM hooks, update the core engine, or refine your AI agent configurations in Severance, those changes propagate to every project on your machine.

![Diagram](https://image.prntscr.com/image/NRZPNEDhRNuTBChupQq_5w.png)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           THE SEVERANCE MODEL                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │                     SEVERANCE (This Repo)                         │     │
│   │   The development & version control environment                   │     │
│   │   ┌─────────────────────────────────────────────────────────────┐ │     │
│   │   │  .claude/cam/cam_core.py    ←── Develop here                │ │     │
│   │   │  .claude/cam/cam.sh         ←── Test here                   │ │     │
│   │   │  ~/.claude/hooks/*.sh       ←── Customize here              │ │     │
│   │   └─────────────────────────────────────────────────────────────┘ │     │
│   └─────────────────────────────┬─────────────────────────────────────┘     │
│                                 │                                           │
│                                 │  cam-sync-template.sh                     │
│                                 │  (Deploy changes globally)                │
│                                 ▼                                           │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │                 ~/.claude/ (Global Installation)                  │     │
│   │   ┌──────────────────┐  ┌──────────────────┐  ┌────────────────┐  │     │
│   │   │  cam-template/   │  │     hooks/       │  │ settings.json  │  │     │
│   │   │  ├── cam_core.py │  │  ├── prompt-cam  │  │ (hook config)  │  │     │
│   │   │  ├── cam.sh      │  │  ├── query-cam   │  └────────────────┘  │     │
│   │   │  └── VERSION     │  │  ├── update-cam  │                      │     │
│   │   └──────────────────┘  │  └── session-*   │                      │     │
│   │                         └──────────────────┘                      │     │
│   └─────────────────────────────┬─────────────────────────────────────┘     │
│                                 │                                           │
│                                 │  init-cam / scaffold-ai.sh                │
│                                 │  (Initialize new projects)                │
│                                 ▼                                           │
│   ┌───────────────────────────────────────────────────────────────────┐     │
│   │              YOUR PROJECTS (Inherit from template)                │     │
│   │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐               │     │
│   │   │  Project A  │  │  Project B  │  │  Project C  │               │     │
│   │   │  .claude/   │  │  .claude/   │  │  .claude/   │               │     │
│   │   │  .ai/       │  │  .ai/       │  │  .ai/       │               │     │
│   │   └─────────────┘  └─────────────┘  └─────────────┘               │     │
│   └───────────────────────────────────────────────────────────────────┘     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Pain Point This Solves

### The Problem

AI coding assistants suffer from **context amnesia**:

- **Session isolation**: Each conversation starts fresh with zero memory of past work
- **Documentation drift**: Agent .md files grow & grow as development continues, causing hallucinations, forgetfulness & more :(
- **Manual context loading**: Developers waste time re-explaining architecture every session
- **Lost learnings**: Debugging insights, architectural decisions, and patterns vanish
- **No semantic search**: Keyword matching misses conceptual relationships

### The Solution

CAM provides a **persistent semantic memory layer** that:

- **Remembers everything**: Every tool call, operation, and conversation turn is embedded and stored
- **Proactively retrieves**: Hooks inject relevant context *before* you ask
- **Learns continuously**: Session summaries accumulate knowledge over time
- **Scales constantly**: O(1) retrieval regardless of project size (~150ms always)
- **Works across agents**: Claude Code, Gemini, and Cursor share the same knowledge base

---

## Quick Start

### 1. Clone Severance

```bash
# Clone the control center
git clone https://github.com/yourusername/severance.git
cd severance

# This is your CAM development environment
```

### 2. Set Up Gemini API Key

```bash
# Required for embeddings
export GEMINI_API_KEY="your-api-key-here"

# Persist it globally
echo "GEMINI_API_KEY=your-key" > ~/.claude/hooks/.env
```


### 3. Deploy to Global Installation

```bash
# Sync Severance's CAM to global template
~/.claude/hooks/cam-sync-template.sh

# This copies:
#   .claude/cam/* → ~/.claude/cam-template/
#   hooks/*.sh    → ~/.claude/hooks/
```

### 4. Configure Claude Code Hooks

Add to `~/.claude/settings.json`:

```json
{
  "hooks": {
    "SessionStart": [{"type": "command", "command": "$HOME/.claude/hooks/session-start.sh"}],
    "UserPromptSubmit": [{"type": "command", "command": "$HOME/.claude/hooks/prompt-cam.sh"}],
    "PreToolUse": [{"type": "command", "command": "$HOME/.claude/hooks/query-cam.sh"}],
    "PostToolUse": [{"type": "command", "command": "$HOME/.claude/hooks/update-cam.sh"}],
    "SessionEnd": [{"type": "command", "command": "$HOME/.claude/hooks/session-end.sh"}]
  }
}
```

**You're done.** Every project now has access to CAM infrastructure.

---

## Initializing CAM in a New Project

### Option 1: CAM Only

```bash
cd your-project
~/.claude/hooks/init-cam.sh
```

This creates:
- `.claude/cam/` with databases and CLI
- Installs dependencies in isolated venv
- Creates `CLAUDE.md` entry point

### Option 2: CAM + .ai Documentation Framework

```bash
cd your-project
~/.claude/hooks/scaffold-ai.sh
# When prompted, choose "Yes" to initialize CAM
```

This creates:
- Everything from Option 1, plus:
- `.ai/` documentation structure
- `CLAUDE.md`, `GEMINI.md`, `.cursor/rules/cursor-rules.mdc`
- Multi-agent support ready

### After Initialization: Analyze Your Codebase

Both scripts output a **bootstrap prompt**. Copy it and paste it to your AI agent:

```
Act as a Principal Software Architect to perform a holistic static analysis
of this codebase, documentation, and configuration files to construct a
comprehensive mental model of the system.

1. **Technology Stack**: Identify languages, frameworks, and infrastructure dependencies
2. **Architecture Map**: Delineate frontend components, backend services, and database entities
3. **Feature Catalog**: Document each module's business purpose, I/O contracts, and dependencies
4. **Data Flow**: Trace interactions from UI through controllers to persistence layers
5. **Technical Spec**: Define function purposes, entity relationships (Mermaid.js), and system workflows

Systematically organize and proactively append all discovered data, architectural
insights, and generated documentation into the /.ai documentation directory.
```

This populates your `.ai/` docs AND feeds CAM its initial knowledge base. After this process is complete you can simply prompt your agent:

```
Systematically append all codebase/project related documentation/knowledge from the /.ai folder into the CAM database, ensure proper vectorization, embeddings, annotations, & graphing.
```

---

## How CAM Works with AI Agents

### Claude Code (Automatic via Hooks)

All 5 hooks execute automatically:

| Hook | Event | Action |
|------|-------|--------|
| `session-start.sh` | SessionStart | Log session begin, retrieve last session summary |
| `prompt-cam.sh` | UserPromptSubmit | Query CAM for intent-based context BEFORE processing |
| `query-cam.sh` | PreToolUse | Retrieve relevant past operations before tool execution |
| `update-cam.sh` | PostToolUse | Embed and annotate the operation that just completed |
| `session-end.sh` | SessionEnd | Generate session summary, store learnings for next time |

**Result**: Zero-effort context. CAM reads your intent and injects relevant history automatically.

### Gemini (Manual via GEMINI.md)

Gemini reads `GEMINI.md` which instructs it to:

```bash
# Query before starting work
./.claude/cam/cam.sh query "your intent" 5

# Add notes after significant work
./.claude/cam/cam.sh annotate "Implemented auth flow using JWT tokens"
```

### Cursor (Manual via .cursor/rules/)

Cursor reads `.cursor/rules/cursor-rules.mdc` which provides:

```bash
# Same CAM commands available
./.claude/cam/cam.sh query "what you're looking for"
./.claude/cam/cam.sh ingest ".ai/" docs
```

---

## The Hook-Based Memory System

### How Knowledge Accumulates

![Diagram](https://image.prntscr.com/image/HYiH9hv3Toyntrj07y5UEQ.png)

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    CONTINUOUS MEMORY LOOP                               │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  SESSION N                                                              │
│  ──────────────────────────────────────────────────────────────────     │
│                                                                         │
│  1. SessionStart → Retrieve last session's learnings                    │
│                    │                                                    │
│                    ▼                                                    │
│  2. User Message → UserPromptSubmit Hook                                │
│                    │                                                    │
│                    ├── Query CAM: "What's relevant to this intent?"     │
│                    ├── Inject context into Claude's context window      │
│                    │                                                    │
│                    ▼                                                    │
│  3. Tool Call → PreToolUse Hook                                         │
│                    │                                                    │
│                    ├── Query CAM: "What should I know before this?"     │
│                    ├── Retrieve past similar operations                 │
│                    │                                                    │
│                    ▼                                                    │
│  4. Tool Executes → [Edit, Write, Bash, Read, etc.]                     │
│                    │                                                    │
│                    ▼                                                    │
│  5. PostToolUse Hook                                                    │
│                    │                                                    │
│                    ├── Embed the operation content (768-dim vector)     │
│                    ├── Annotate with metadata (file, type, session)     │
│                    ├── Add to knowledge graph (relationships)           │
│                    │                                                    │
│                    ▼                                                    │
│  6. Session End → SessionEnd Hook                                       │
│                    │                                                    │
│                    ├── Generate session summary                         │
│                    ├── Store learnings for next session                 │
│                    └── Update knowledge graph clusters                  │
│                                                                         │
│  ──────────────────────────────────────────────────────────────────     │
│  SESSION N+1                                                            │
│  ──────────────────────────────────────────────────────────────────     │
│                                                                         │
│  SessionStart → Retrieves Session N's summary                           │
│              → "Last time you worked on authentication..."              │
│              → Continuity achieved                                      │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### What Gets Stored

| Source Type | Examples | Hooks |
|-------------|----------|-------|
| `code` | File edits, new functions, refactors | PostToolUse (Edit, Write) |
| `operation` | Bash commands, test runs, builds | PostToolUse (Bash) |
| `docs` | Documentation, README updates | PostToolUse, manual ingest |
| `conversation` | Session summaries, learnings | SessionEnd |
| `external` | Manually added context | `cam.sh annotate` |

---

## CAM Commands Reference

```bash
# Status and diagnostics
./cam.sh version              # Show CAM version
./cam.sh stats                # Database statistics

# Semantic search
./cam.sh query "text" [k]     # Find top K relevant items (default k=5)

# Content management
./cam.sh ingest <file> <type> # Add file to CAM (type: code|docs|operation)
./cam.sh annotate "<text>"    # Add manual annotation

# Knowledge graph
./cam.sh graph build          # Build graph from embeddings
./cam.sh graph stats          # Graph statistics

# Evaluation & benchmarks
./cam.sh eval all             # Run full evaluation suite
./cam.sh benchmark all        # Run DMR + LoCoMo benchmarks

# Maintenance
./cam.sh upgrade              # Update from template
./cam.sh rebuild graph        # Rebuild knowledge graph
```

---

## Customizing Severance

### Modifying Hooks

Edit hooks in `~/.claude/hooks/`:

```bash
# Example: Add custom filtering to query results
vim ~/.claude/hooks/prompt-cam.sh
```

### Modifying Core Engine

Edit in Severance, then deploy:

```bash
cd severance/.claude/cam
vim cam_core.py

# Test locally
./cam.sh eval all

# Deploy globally
~/.claude/hooks/cam-sync-template.sh
```

### Adding New Hooks

1. Create script in `~/.claude/hooks/`
2. Add to `~/.claude/settings.json`
3. Sync to template: `cam-sync-template.sh`

---

## Architecture Deep Dive

### Three-Database Design

```
vectors.db      # 768-dim embeddings (Gemini embedding-001)
    │           # Content storage with source tracking
    │
metadata.db     # JSON annotations per embedding
    │           # Tags, confidence scores, relationships
    │
graph.db        # Knowledge graph
                # Nodes (concepts) + Edges (temporal/semantic/causal)
```

### Query Flow (150ms regardless of DB size)

```
User: "How does authentication work?"
          │
          ▼
    ┌─────────────┐
    │ Embed Query │ → Gemini API → 768-dim vector
    └──────┬──────┘
           │
           ▼
    ┌─────────────┐
    │Cosine Search│ → Compare against all stored embeddings
    └──────┬──────┘
           │
           ▼
    ┌─────────────┐
    │  Top-K Sort │ → Rank by similarity score
    └──────┬──────┘
           │
           ▼
    ┌─────────────┐
    │  Enrich     │ → Add metadata, graph context
    └──────┬──────┘
           │
           ▼
    Return contextualized results
```

---

## Validation Metrics

| Metric | Score | Interpretation |
|--------|-------|----------------|
| Embedding Quality (STS) | 0.9783 | Excellent alignment with human judgment |
| Retrieval Accuracy (nDCG@5) | 1.0000 | Perfect ranking in tests |
| Graph Quality | 0.9070 | Strong concept clustering |
| **Overall Health** | **98.3%** | **Production Ready** |

---

## Project Structure

```
severance/
├── .ai/                          # Documentation framework
│   ├── core/                     # Architecture, tech stack, overview
│   ├── development/              # Workflow, testing patterns
│   ├── patterns/                 # Database, API, security patterns
│   └── meta/                     # Documentation maintenance guides
├── .claude/
│   └── cam/                      # CAM core (development version)
│       ├── cam_core.py           # Python engine (3,300 lines)
│       ├── cam.sh                # CLI wrapper
│       ├── vectors.db            # Embedding storage
│       ├── metadata.db           # Annotation storage
│       ├── graph.db              # Knowledge graph
│       └── test_*.py             # Test suite
├── CLAUDE.md                     # Claude Code entry point
├── GEMINI.md                     # Gemini entry point
└── README_GITHUB.md              # This file

~/.claude/                        # Global installation (deployed from Severance)
├── cam-template/                 # Template for new projects
├── hooks/                        # Global hook scripts
│   ├── prompt-cam.sh             # UserPromptSubmit
│   ├── query-cam.sh              # PreToolUse
│   ├── update-cam.sh             # PostToolUse
│   ├── session-start.sh          # SessionStart
│   ├── session-end.sh            # SessionEnd
│   ├── init-cam.sh               # Project initialization
│   └── scaffold-ai.sh            # .ai/ framework scaffolding
├── settings.json                 # Hook configuration
└── CLAUDE.md                     # Global AI instructions
```

---

## The `.ai/` Documentation Framework

### Purpose: Single Source of Truth

The `.ai/` directory is a **structured documentation framework** that serves as the **single source of truth** for all project knowledge. Instead of scattering documentation across READMEs, inline comments, and agent-specific files, everything lives in one canonical location.

![Diagram](https://image.prntscr.com/image/IgdUSh4URoS6ekn7UjSZrA.png)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    THE .ai/ DOCUMENTATION MODEL                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                     AGENT ENTRY POINTS                              │   │
│   │   ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐     │   │
│   │   │  CLAUDE.md  │  │  GEMINI.md  │  │ .cursor/rules/*.mdc     │     │   │
│   │   │  (Claude)   │  │  (Gemini)   │  │ (Cursor)                │     │   │
│   │   └──────┬──────┘  └──────┬──────┘  └───────────┬─────────────┘     │   │
│   │          │                │                     │                   │   │
│   │          │                │                     │                   │   │
│   │          └────────────────┼─────────────────────┘                   │   │
│   │                           │                                         │   │
│   │                           │  All agents route to .ai/               │   │
│   │                           ▼                                         │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                   .ai/ (SINGLE SOURCE OF TRUTH)                     │   │
│   │                                                                     │   │
│   │   ┌─────────────────────────────────────────────────────────────┐   │   │
│   │   │  README.md ←── Navigation hub for all documentation         │   │   │
│   │   └─────────────────────────────────────────────────────────────┘   │   │
│   │                                                                     │   │
│   │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌──────────┐   │   │
│   │   │    core/    │  │development/ │  │  patterns/  │  │  meta/   │   │   │
│   │   ├─────────────┤  ├─────────────┤  ├─────────────┤  ├──────────┤   │   │
│   │   │ tech-stack  │  │  workflow   │  │  database   │  │ sync-    │   │   │
│   │   │ architecture│  │  testing    │  │  api        │  │  guide   │   │   │
│   │   │ overview    │  │             │  │  security   │  │ maintain │   │   │
│   │   │ deployment  │  │             │  │  frontend   │  │          │   │   │
│   │   └─────────────┘  └─────────────┘  └─────────────┘  └──────────┘   │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│                                    │                                        │
│                                    │  CAM indexes .ai/ for                  │
│                                    │  semantic queries                      │
│                                    ▼                                        │
│   ┌─────────────────────────────────────────────────────────────────────┐   │
│   │                      CAM SEMANTIC LAYER                             │   │
│   │   .ai/ docs → embeddings → vectors.db → semantic search             │   │
│   │                                                                     │   │
│   │   Result: Ask "how does auth work?" and get                         │   │
│   │   relevant docs + code + past operations                            │   │
│   └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Structure and Categories

| Directory | Purpose | Examples |
|-----------|---------|----------|
| `core/` | Foundational project information | `technology-stack.md` (version numbers), `application-architecture.md`, `project-overview.md` |
| `development/` | How to work on the project | `development-workflow.md`, `testing-patterns.md` |
| `patterns/` | Code patterns and best practices | `database-patterns.md`, `api-and-routing.md`, `security-patterns.md` |
| `meta/` | Documentation maintenance guides | `maintaining-docs.md`, `sync-guide.md` |

### Key Principles

1. **Single Location**: Each fact exists in ONE place. Version numbers **only** in `core/technology-stack.md`. Architecture details **only** in `core/application-architecture.md`.

2. **Cross-Reference, Never Duplicate**: If you need to mention a version number elsewhere, reference it: "See [technology-stack.md](core/technology-stack.md) for versions."

3. **Agent Entry Points Route to .ai/**: `CLAUDE.md`, `GEMINI.md`, and `.cursor/rules/cursor-rules.mdc` are **thin routing files**. They contain minimal instructions and point agents to `.ai/` for everything else.

### The Meta Layer: Self-Maintaining Documentation

The `meta/` directory contains guides for maintaining the documentation itself:

**`meta/maintaining-docs.md`** - When and how to update documentation:
- Add new docs when a pattern appears in 3+ files
- Modify existing docs when better examples exist in codebase
- Always use real code examples, not theoretical ones
- Quality checklist before committing

**`meta/sync-guide.md`** - How to keep everything synchronized:
- Where to make changes (single source locations)
- Update checklist for significant changes
- Cross-references that must stay accurate

### Proactive Update Cycle with Agents

The `.ai/` framework is designed for **conjunctive** (working together) and **proactive** (anticipating needs) updates between AI agents and CAM:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    PROACTIVE DOCUMENTATION LOOP                         │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  1. AGENT WORKS ON CODEBASE                                             │
│     │                                                                   │
│     ├── Implements new feature                                          │
│     ├── Fixes bug                                                       │
│     ├── Refactors code                                                  │
│     │                                                                   │
│     ▼                                                                   │
│  2. AGENT NOTICES DOCUMENTATION GAP                                     │
│     │                                                                   │
│     ├── "This pattern isn't documented"                                 │
│     ├── "The architecture doc is outdated"                              │
│     ├── "This workflow isn't explained"                                 │
│     │                                                                   │
│     ▼                                                                   │
│  3. AGENT PROACTIVELY UPDATES .ai/                                      │
│     │                                                                   │
│     ├── Updates existing doc: .ai/patterns/database-patterns.md         │
│     ├── Creates new doc if needed: .ai/patterns/new-pattern.md          │
│     ├── Updates .ai/README.md navigation                                │
│     │                                                                   │
│     ▼                                                                   │
│  4. CAM HOOKS CAPTURE THE CHANGE                                        │
│     │                                                                   │
│     ├── PostToolUse → Embeds the .ai/ file edit                         │
│     ├── Creates 768-dim vector of the documentation                     │
│     ├── Adds to knowledge graph with relationships                      │
│     │                                                                   │
│     ▼                                                                   │
│  5. FUTURE QUERIES INCLUDE NEW KNOWLEDGE                                │
│     │                                                                   │
│     ├── UserPromptSubmit → "How does X work?"                           │
│     ├── CAM retrieves updated .ai/ docs semantically                    │
│     └── Agent receives current context, not stale info                  │
│                                                                         │
│  ═══════════════════════════════════════════════════════════════════    │
│  RESULT: Documentation stays current because:                           │
│  - Agents are instructed to update .ai/ when they notice gaps           │
│  - CAM automatically indexes every .ai/ change                          │
│  - Future sessions receive updated context via hooks                    │
└─────────────────────────────────────────────────────────────────────────┘
```

### How Agents Use .ai/

**Claude Code** (automatic):
- Hooks inject relevant `.ai/` docs before processing prompts
- `CLAUDE.md` instructs: "Proactive updates: Update `.ai/` when you notice gaps"
- PostToolUse hook captures every `.ai/` edit for CAM

**Gemini** (manual):
- `GEMINI.md` provides quick routes to `.ai/` files
- Instructed to query CAM before work: `./.claude/cam/cam.sh query "intent"`
- Instructed to update `.ai/` proactively
- Re-index after updates: `./.claude/cam/cam.sh ingest ".ai/" docs`

**Cursor** (manual):
- `.cursor/rules/cursor-rules.mdc` references `.ai/` structure
- Same manual CAM workflow as Gemini

### Multi-Agent Consistency

Because all agents read from the same `.ai/` source:

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Claude    │     │   Gemini    │     │   Cursor    │
│   Session   │     │   Session   │     │   Session   │
└──────┬──────┘     └──────┬──────┘     └──────┬──────┘
       │                   │                   │
       │                   │                   │
       └───────────────────┼───────────────────┘
                           │
                           │  All read same docs
                           ▼
                    ┌─────────────┐
                    │    .ai/     │
                    │  (unified)  │
                    └─────────────┘
                           │
                           │  All indexed by
                           ▼
                    ┌─────────────┐
                    │    CAM      │
                    │  (shared)   │
                    └─────────────┘

RESULT: Switch between Claude, Gemini, and Cursor mid-project.
        All agents have identical project knowledge.
```

### Initializing .ai/ in Your Project

Run `scaffold-ai.sh` to create the full `.ai/` framework:

```bash
cd your-project
~/.claude/hooks/scaffold-ai.sh
```

This creates:
- `.ai/` directory with all subdirectories
- `README.md` navigation hub
- Placeholder files in each category
- `CLAUDE.md`, `GEMINI.md`, `.cursor/rules/cursor-rules.mdc` entry points

Then run the bootstrap prompt to populate `.ai/` with your codebase analysis.

---

## Recap

Clone Severance in any folder directory `~/Documents/Projects/` <- you will treat this as YOUR `version control`, make changes how you see fit. 

In your project directory (no pre-existing cam infra) → `~/.claude/hooks/init-cam.sh` | creates CAM infra for your project

In your project directory (no pre-existing cam infra) → `~/.claude/hooks/scaffold-ai.sh` | scaffolds /.ai doc system for your project

In your project directory .claude/cam** folder (yes pre-exisiting cam infra) `~/Documents/Projects/MyAwesomeAIWrapper/.claude/cam` → `./cam.sh upgrade --force`

Utilize Claude/Gemini/Cursor to update the CAM infra db, & update/iterate the /.ai doc system proactively, ask questions, whatever you see fit.

Claude Hooks should handle the rest accordingly.

---

## FAQ

**Q: Do I need to clone Severance into every project?**

A: No. Clone Severance once, customize it, then run `cam-sync-template.sh` to deploy globally. New projects use `init-cam.sh` to get the template. (must be in root project dir)

**Q: What if I don't want all 5 hooks?**

A: Edit `~/.claude/settings.json` to enable only the hooks you want. `prompt-cam.sh` (UserPromptSubmit) alone provides significant value.

**Q: Does CAM work offline?**

A: Partially. Existing embeddings can be queried offline, but new embeddings require Gemini API access. Operations continue and are queued for embedding when connectivity returns.

**Q: How much does CAM cost?**

A: Gemini API is free tier for most development usage. Each embedding call (~768 dim) costs fractions of a cent. A typical session generates 10-50 embeddings.

**Q: Can I use a different embedding model?**

A: Yes. Modify `cam_core.py` to use OpenAI, Cohere, or local models. The interface expects a function that returns a numpy array.

---

## Contributing

1. Fork Severance
2. Make changes in your fork
3. Test with `./cam.sh eval all`
4. Submit PR with description of changes

---

![Diagram](https://image.prntscr.com/image/tJ3kSIPOSRSLBw6pLR0VUQ.png)

#
# Notice
I realize this may not be the best way to go about it. I don't know if it's placebo, but it seems to help out a lot. I've only been able to test it in a few projects: Severance, a multi-API aggregate routing tool, and case study projects (non-complex) & it seems to steer the models in the right direction with very little context. 

I intended this project to essentially query/read/write to the memory layer any time it ran into new information, bad code, good code, new implementation, planning, researching, diagnosing, tool calling, conversation turns, prompts, etc. Hence a fully persistent memory layer loop that essentially forces Claude/your agent(s) to query the database pre and post planning, pre and post tool calls/researching/diagnosing/etc., giving the agent up to date and relevant context for the prompt/task at hand.

I haven't been able to create evals for token consumption just yet, for right now in the LLM space, I think we are at the point of determining what is more viable for agentic development, how a user/consumer orchestrates an agent to utilize tools or the tools/features/capabilities that a agent is shipped with. 

`The latest of implementation of Opus 4.5 and the multi-layer code execution feature for MCP server tool calling is a step in the 'right' abstraction of token performant models, so part of the purpose of open sourcing this project is to see if this could be the abstraction that AI engineers/software developers are looking for when it comes to a persistent and contextually aware agent/llm model.`

#
# Disclaimer
> [!WARNING]
> `Please backup your global ~/.claude/ folder, .claude folder, CLAUDE.md, GEMINI.md, & .cursor folder. Initializing will overwrite files.
I suggest "testing" CAM infra in a test project to understand how it works, play around a bit with it and move on from there.`

If you have custom workflows/directions/specific instructions in any of these files, it's advised to append it to the agent related .md files that are scaffolded post initialization. 

One last thing, i'm not comfortable with the state that it's at right now, but when I get some things fine tuned, straightened out - it will be open source.


#
## Credit
`.ai scaffolding system` | [Dev](https://github.com/andrasbacsai) [Repo of Inspiration](https://github.com/coollabsio/coolify)

#
## Mentions
`Anthropic Claude` | [Meet Opus](https://www.anthropic.com/news/claude-opus-4-5)

`Google Gemini` | [Meet Gemini](https://ai.google.dev/gemini-api/docs/gemini-3?thinking=high)

 
