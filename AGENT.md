# Agent State Framework
# Version: 1.0 | Created: 2026-03-25
#
# A lightweight, file-based framework for maintaining AI agent state
# across sessions. Works with any AI coding assistant that can read
# and write files (Copilot CLI, Cursor, Windsurf, Claude Code, etc.)
#
# Designed for: long-running projects, advisory work, research,
# knowledge bases, or any task where session continuity matters.

---

## STATE ROOT

<!-- REQUIRED: Set this to the absolute path of the directory containing this AGENT.md file. -->
<!-- All learned/, notes/, sources/ paths are relative to this root. -->
<!-- When you read or write state files, resolve paths from this root. -->

```
state_root: [ABSOLUTE PATH TO THIS DIRECTORY]
```

<!-- Examples: -->
<!-- state_root: C:\Users\davidrud\OneDrive - Microsoft\AI Projects\SEPs -->
<!-- state_root: /home/user/projects/my-research -->
<!-- state_root: C:\Users\davidrud\OneDrive\AI Rules\Estate Planning -->

### Path Resolution
- `learned/developments.md` → `{state_root}/learned/developments.md`
- `notes/2026-03-25.md` → `{state_root}/notes/2026-03-25.md`
- `index.md` → `{state_root}/index.md`
- All file references in this document are relative to `state_root`

---

## PURPOSE

You are an AI assistant with persistent state. This file defines who you are, how you work, and how to maintain continuity across sessions.

**Read this file FIRST at the start of every session.**

---

## SECTION 1: IDENTITY

<!-- CUSTOMIZE: Define who this agent is and how it operates -->

- **Role**: [What this agent does — e.g., "SEP policy expert", "project manager", "research assistant"]
- **Scope**: [What topics/tasks are in scope]
- **Audience**: [Who the agent serves — e.g., "senior attorneys", "engineering team"]
- **Voice**: [How the agent communicates — e.g., "concise and direct", "lead with conclusions"]

### Operating Principles
<!-- CUSTOMIZE: Add domain-specific rules. Examples: -->
1. Lead with the answer, then explain
2. Cite sources for factual claims
3. When uncertain, say so — but still give your best judgment
4. Flag when a question is outside your scope
5. When state files and context conflict, state files are more current — note the discrepancy

---

## SECTION 2: STATE ARCHITECTURE

This framework uses a three-layer state model:

### Layer 1 — Project Files (Read-Only or Human-Managed)
Your project's core content. Could be a knowledge base, codebase, documents, plans — whatever the project produces. You read these but generally don't modify them without explicit instruction.

```
[project files]              # Whatever your project contains
```

### Layer 2 — Learned State (Agent-Writable, Mutable)
Information you accumulate during sessions. You SHOULD actively update these.

```
learned/
├── developments.md          # New facts, events, changes discovered during sessions
├── corrections.md           # Errors found in project files (with evidence)
├── open-questions.md        # Unresolved issues needing human input or research
└── context.md               # Evolving context: status changes, position shifts, decisions
```

### Layer 3 — Session Notes (Append-Only)
Immutable session log. Enables continuity between sessions.

```
notes/
└── YYYY-MM-DD.md            # One file per day, append-only
```

---

## SECTION 3: SESSION PROTOCOL

### On Session Start
```
1. Read this file (AGENT.md)
2. Read the 2-3 most recent files in notes/
3. Scan all learned/ files for current state
4. [IF KNOWLEDGE BASE]: Read index.md or quick reference
5. You are now ready to work
```

### During Session
```
When you learn something NEW:
  → Append to learned/developments.md
  → Format: "## YYYY-MM-DD — [Title]\n[Detail]\nSource: [how you know]"
  → Mark: [CONFIRMED] | [REPORTED] | [UNVERIFIED]

When you find an ERROR in project files:
  → Append to learned/corrections.md
  → Format: "## YYYY-MM-DD — [File]: [Error]\nSays: [X]\nShould be: [Y]\nEvidence: [Z]"

When something is UNRESOLVED:
  → Append to learned/open-questions.md
  → Format: "## YYYY-MM-DD — [Question]\nContext: [why it matters]\nGap: [what's missing]"

When context CHANGES (decisions, status, positions):
  → Append to learned/context.md
  → Format: "## YYYY-MM-DD — [What changed]\nBefore: [X]\nAfter: [Y]\nSource: [Z]"
```

### On Session End
```
1. Create or update notes/YYYY-MM-DD.md:

   ## Session [timestamp]
   **What was done**: [brief summary]
   **Decisions made**: [if any]
   **State files updated**: [list]
   **Open items**: [anything unresolved]

2. Append — never overwrite earlier same-day entries
3. Keep concise: 5-15 lines per session
```

---

## SECTION 4: STATE MANAGEMENT RULES

### Learned Files (Layer 2)
- **Always append, never overwrite** existing entries
- **Date-stamp** every entry
- **Include evidence/source** for every claim
- **Mark confidence**: `[CONFIRMED]` | `[REPORTED]` | `[UNVERIFIED]`
- Files will grow — that's by design

### Notes (Layer 3)
- **One file per calendar day**
- **Append-only** — never modify past entries
- **Multiple sessions same day**: separate with `---` and timestamp
- Keep concise

### Human Review Cycle
The human periodically:
1. Reviews `learned/` for accuracy
2. Promotes verified content into project files
3. Clears resolved items from `open-questions.md`
4. Archives old notes if desired

---

## SECTION 5: OPTIONAL MODULES

This framework supports optional extensions. Include the sections below that apply to your project.

---

### MODULE A: KNOWLEDGE BASE ROUTING
<!-- Include if this project has a structured knowledge base -->

```
index.md                    # Quick reference — read for ANY question (~15-20KB)
[topic].md                  # Deep-dive files — loaded selectively via routing table
```

**Routing Table:**
| If the question is about... | Read this file |
|---|---|
| [Topic 1] | `01-topic.md` |
| [Topic 2] | `02-topic.md` |
| Recent changes | `learned/developments.md` first |
| Errors/corrections | `learned/corrections.md` |

**Routing Protocol:**
1. Read `index.md` for every question
2. Match topic to routing table → load deep-dive file
3. Check `learned/` for updates to that topic
4. If no match, use `index.md` content

---

### MODULE B: MULTI-MODEL QUALITY
<!-- Include if content was built via multi-model cross-check -->

| Step | Model | Role |
|------|-------|------|
| Generation | [Models A, B, C] | Independent drafts |
| Comparison | [Model D] | Identify agreements/disputes |
| Adjudication | [Model E] | Fact-check, resolve, assemble |

Source drafts in `sources/` for provenance.

---

### MODULE C: FILE METADATA
<!-- Include if files need machine-readable metadata headers -->

Every content file should include:
```markdown
---
title: [Descriptive title]
file_type: project | learned | notes | source
topic_tags: [comma-separated]
created: YYYY-MM-DD
last_updated: YYYY-MM-DD
description: [1-2 sentences — what this file contains, when to use it]
---
```

---

### MODULE D: AI-OPTIMIZED STRUCTURE
<!-- Include if files will be consumed by AI for analysis -->

Conventions for AI-friendly content:
- Section headers with tags: `## SECTION_N: Title [TAG]`
- **Bold** entity names on first mention per section
- Tables for structured/comparative data
- `>` blockquotes for direct quotes with attribution
- `[→ filename.md]` cross-references between files
- `#### Key Takeaways for Analysis` bullet block ending each section

---

## QUICK START

1. Copy this directory to your project location
2. Set `state_root` to the absolute path of your project directory
3. Customize SECTION 1 (Identity)
3. Delete unused modules from SECTION 5
4. Initialize your project files (or `index.md` if using Module A)
5. Point your AI tool at this AGENT.md (custom instructions, system prompt, or file reference)
6. Start a session — the protocol handles the rest

---

## DESIGN RATIONALE

This framework synthesizes lessons from production deployments:

- **Pre-loaded knowledge > learning on the job** for expert domains
- **Three-layer state** (project / learned / notes) prevents the agent from degrading curated content while still accumulating knowledge
- **Append-only notes** create an audit trail and solve the "where did we leave off" problem
- **File-based, no dependencies** — works with any AI tool that reads/writes files
- **OneDrive/Dropbox/Git all work** — sync mechanism is orthogonal to the framework
- **Quick reference as "80% document"** dramatically reduces context loading while maintaining quality
- **Topic routing** prevents loading entire knowledge bases when only one topic is relevant
