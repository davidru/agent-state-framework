# Agent State Framework

A lightweight, file-based framework for maintaining AI agent state across sessions. No databases, no vector stores, no dependencies — just markdown files and a protocol.

## What It Solves

AI coding assistants (Copilot CLI, Cursor, Windsurf, Claude Code, etc.) lose all context between sessions. This framework gives them persistent memory through a simple file structure and session protocol.

## Use Cases

- **Knowledge bases** — Expert systems with topic routing and curated content
- **Advisory work** — Track decisions, positions, and evolving context across engagements
- **Research projects** — Accumulate findings, flag gaps, maintain continuity
- **Long-running tasks** — Any multi-session work where "where did we leave off?" matters

## Structure

```
your-project/
├── AGENT.md                    # Identity, protocol, state rules (read every session)
├── learned/                    # Agent-writable mutable state
│   ├── developments.md         #   New facts and events
│   ├── corrections.md          #   Errors found in project files
│   ├── open-questions.md       #   Unresolved issues
│   └── context.md              #   Status changes, decisions, evolving context
├── notes/                      # Append-only session logs
│   └── YYYY-MM-DD.md           #   One per day, never modified after written
├── sources/                    # Raw materials (optional, read-only)
└── [your project files]        # Whatever your project produces
```

## Three-Layer State Model

| Layer | Contents | Who Writes | Mutability |
|-------|----------|-----------|------------|
| **Project** | Core content (KB, code, docs) | Human (or agent with permission) | Controlled |
| **Learned** | Session discoveries, errors, questions | Agent | Append-only |
| **Notes** | Session logs | Agent | Immutable once day ends |

The agent can never degrade your curated content. New knowledge goes into `learned/`, where you review and promote it on your schedule.

## Session Protocol

```
START:   Read AGENT.md → Read recent notes/ → Scan learned/
DURING:  Work normally. Append to learned/ when you discover new info, errors, or gaps.
END:     Append session summary to notes/YYYY-MM-DD.md
```

## Optional Modules

AGENT.md includes plug-in modules you can enable or delete:

| Module | Purpose |
|--------|---------|
| **A: KB Routing** | Topic-based routing table for knowledge bases |
| **B: Multi-Model Quality** | Track provenance when multiple AI models contribute |
| **C: File Metadata** | Machine-readable YAML headers for AI retrieval |
| **D: AI-Optimized Structure** | Conventions for AI-friendly content formatting |

## Quick Start

```bash
# Clone the template
git clone https://github.com/YOUR_USERNAME/agent-state-framework.git my-project

# Customize
# 1. Edit AGENT.md Section 1 (Identity)
# 2. Delete unused modules from Section 5
# 3. Add your project files
# 4. Point your AI tool at AGENT.md
```

## Works With

Any AI coding assistant that can read and write files:
- GitHub Copilot CLI
- Cursor
- Windsurf
- Claude Code (Claude CLI)
- Cline
- Aider
- Any tool that supports custom instructions or system prompts referencing local files

## Sync

The framework is file-based and sync-agnostic:
- **OneDrive/Dropbox** — auto-sync, zero config
- **Git** — version control, diff review, PR-based promotion
- **Local** — works offline, no network needed

## Design Principles

1. **Files over databases** — readable, portable, versionable
2. **Append-only state** — agent can't destroy what it's learned
3. **Curated content is sacred** — agent documents errors, human fixes them
4. **Protocol over tooling** — works with any AI tool, no vendor lock-in
5. **Quick reference pattern** — load 15KB, answer 80% of questions

## Origin

Synthesized from production deployments including a 76,000-email expert avatar system, multi-model knowledge base pipelines, and the [Agent Kernel](https://github.com/oguzbilgic/agent-kernel) pattern. See AGENT.md "Design Rationale" for details.

## License

MIT
