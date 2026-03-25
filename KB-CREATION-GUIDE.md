# Knowledge Base Creation Guide
# Version: 1.0 | Created: 2026-03-25
#
# Generic instructions for building a high-quality, AI-optimized knowledge base.
# Designed to be included in AGENT.md or referenced by any AI coding assistant.
#
# When the agent detects this is a new/unconfigured project, it runs the
# SETUP WIZARD (Section 1) to configure everything through conversation.

---

## HOW TO USE THIS FILE

Add this to your AGENT.md under a new module, OR include it as a standalone
file referenced in your custom instructions. When the agent starts a session
and finds an unconfigured KB project (no index.md, empty routing table, or
`setup_complete: false` in AGENT.md), it should run the Setup Wizard.

---

## SECTION 1: SETUP WIZARD

When starting a new KB project, ask the user the following questions IN ORDER.
Use the answers to configure AGENT.md, generate the file structure, and
begin content creation. Ask one question at a time. Offer sensible defaults.

### Phase 1 — Scope and Identity

**Q1: What is this knowledge base about?**
> Get a 1-2 sentence description of the domain.
> Example: "Standards essential patents — legal, policy, and economic dimensions"

**Q2: Who is the target audience?**
> Choices: [Senior practitioners/experts, Mid-level professionals, General/mixed audience, Internal team, External/public]
> This determines depth, terminology, and assumed knowledge level.

**Q3: What is the primary use case?**
> Choices: [Reference/lookup — answer specific questions, Analysis — support decision-making and reasoning, Advisory — respond in an expert voice on behalf of someone, Training — teach the domain to others, Research — accumulate and synthesize findings]
> Multiple selections allowed. Determines voice, structure, and depth.

**Q4: Are there specific entities to track?**
> Examples: companies, people, organizations, government bodies, court cases
> If yes, get a list. These become the entity tags used throughout the KB.

**Q5: What source types are authoritative for this domain?**
> Prompt for specifics: academic journals, government publications, court decisions,
> industry reports, company statements, think tanks, etc.
> Name specific institutions if possible (e.g., "Harvard Law Review, NBER, USPTO")

### Phase 2 — Structure

**Q6: How many major topics do you expect?**
> Help the user sketch a topic list. Suggest 8-15 for most domains.
> Each topic becomes a deep-dive file.
> Offer to generate a proposed topic list based on Q1 and let user edit.

**Q7: Do you have existing source materials?**
> Types: documents, PDFs, websites, emails, transcripts, datasets
> If yes: ask where they are (paths, URLs). These go in sources/.
> If no: the KB will be built primarily via web research.

**Q8: Should this KB track evolving positions or is it point-in-time?**
> If evolving: enable full state management (learned/, notes/)
> If point-in-time: simpler structure, state layer optional

**Q9: Will multiple AI models be used to build or verify content?**
> If yes: enable multi-model quality protocol (Module B in AGENT.md)
> If no: single-model generation with web-search fact-checking

### Phase 3 — Generation Strategy

**Q10: What is the target depth?**
> Choices: [Comprehensive — 60-100KB total (recommended for expert audiences),
>           Moderate — 30-60KB total,
>           Focused — 15-30KB total]

**Q11: Where should the KB be stored?**
> Get the absolute path. This becomes `state_root` in AGENT.md.
> Create the directory structure immediately after this answer.

### After Setup — Generate Configuration

Using the answers above, the agent should:

1. **Configure AGENT.md**:
   - Set `state_root`
   - Fill in Identity (role, audience, voice, scope) from Q1-Q3
   - Build the routing table from Q6
   - Enable/disable optional modules based on Q8-Q9
   - Mark `setup_complete: true`

2. **Create directory structure**:
   ```
   {state_root}/
   ├── AGENT.md              (configured)
   ├── index.md              (stub with topic map from Q6)
   ├── 01-[topic].md         (empty stubs for each topic)
   ├── ...
   ├── learned/              (initialized with empty starter files)
   │   ├── new-developments.md
   │   ├── corrections.md
   │   ├── open-questions.md
   │   └── position-updates.md  (if Q4 entities exist)
   ├── notes/
   └── sources/              (move/copy materials from Q7 here)
   ```

3. **Generate the content creation prompt**:
   Construct a comprehensive prompt incorporating all answers, formatted for
   the generation strategy in Section 2 below. Present it to the user for
   review before starting generation.

---

## SECTION 2: CONTENT GENERATION BEST PRACTICES

### Prompt Construction

Build the generation prompt using this template, filled from Setup Wizard answers:

```
Create a comprehensive, deeply detailed knowledge base on [Q1: topic].

TARGET AUDIENCE: [Q2: audience description]. Calibrate depth, terminology,
and assumed knowledge accordingly.

USE CASE: [Q3: primary use case(s)].

SOURCE QUALITY: Use only high-quality, trusted sources including [Q5: source types].
Prioritize primary sources (court decisions, testimony, published data) over
secondary analysis.

ENTITIES TO TRACK: [Q4: entity list]. Include detailed profiles with specific
positions, data, and attributed quotes for each.

STRUCTURE: Create [Q6: count] topic files:
[Q6: topic list with descriptions]

SOURCE MATERIALS: [Q7: description of available materials and paths]

DEPTH TARGET: [Q10: target depth]

[If Q9 = multi-model]:
Generate independently using [Model A], then [Model B], then [Model C].
Use [Model D] to compare, fact-check, and assemble the authoritative version.
No model grades its own work.
```

### Generation Strategy by Size

**Small KB (15-30KB):**
- Generate in a single pass
- One agent, one file, then split into topics

**Medium KB (30-60KB):**
- Generate in 2 chunks (sections 1-6, then 7-12)
- Save after each chunk to prevent data loss

**Large KB (60-100KB+):**
- Generate in 3-4 chunks
- Save after each chunk
- Consider multi-model cross-check for quality

### Multi-Model Pipeline (when enabled)

```
Step 1: Generate KB independently with 2-3 different models
        → Save each as sources/kb_[model].md
Step 2: Structural comparison (use a model that generated)
        → Identify: agreements, disagreements, gaps
Step 3: Adjudication (use a model that did NOT generate)
        → Fact-check disputed claims via web search
        → Resolve conflicts with sourced reasoning
Step 4: Final assembly (same adjudication model)
        → Build authoritative index.md + topic files
        → Document provenance for disputed items
```

### Content Quality Rules

1. **Specify audience in the prompt** — "senior practitioners" vs. "general audience"
   produces fundamentally different output
2. **Name authoritative sources explicitly** — "Harvard Law Review, NBER, USPTO"
   not just "reputable sources"
3. **Demand specific data** — numbers, rates, dates, named judges, holdings
   not just "the court found in favor of..."
4. **Include both sides** — for any debate, require positions supporting AND
   opposing, with equal depth and rigor
5. **Require primary sources** — PDFs, testimony, published rate cards are
   stronger than commentary about them
6. **Request entity profiles** — for each tracked entity, require: role, position,
   key quotes, data points, litigation history

### Chunked Writing Protocol

To avoid agent timeouts (503s) on large KBs:

```
Chunk 1: Create file → metadata + TOC + sections 1-4
         SAVE checkpoint
Chunk 2: Append sections 5-8
         SAVE checkpoint
Chunk 3: Append sections 9-12
         SAVE checkpoint
Chunk 4: Append sections 13-end + bibliography
         SAVE final
```

- If a chunk fails, restart from last successful checkpoint
- Agent running >60 minutes on a single chunk = probably stuck → kill and restart
- Max 2-3 concurrent generation agents (API saturation above that)

---

## SECTION 3: AI-OPTIMIZATION CONVENTIONS

Apply these conventions to all generated content for optimal AI retrieval:

### File Level
- YAML metadata header on every file (title, type, tags, dates, description)
- Files stay under 30KB each for reliable context loading
- One topic per file — don't combine unrelated subjects

### Section Level
- Machine-readable headers: `## SECTION_N: Title [TAG]`
- Entity subsection tags: `### CASE: Name [CASE_TAG]`
- Cross-references: `[→ filename.md, SECTION_N]`
- `#### Key Takeaways for Analysis` bullet block ending each major section

### Content Level
- **Bold** entity names on first mention per section
- Tables for structured and comparative data (AI parses tables well)
- Blockquotes `>` for direct quotes with attribution
- Inline source references in parentheses: (Author, Year) or (Court, Date)
- Present opposing positions with labeled headers, not interleaved

### Index File
- ~15-20KB — the "80% document"
- Contains: overview, key facts table, topic map, section summaries, quick answer guide
- Designed to be loaded for EVERY question — must be compact enough for context window

---

## SECTION 4: POST-GENERATION AUDIT

After generating a KB, audit for:

### Consistency Check
- [ ] Are all topic files at comparable depth? (10× quality cliff = critical failure)
- [ ] Do all files follow the same structural conventions?
- [ ] Are entity names consistent across files?

### Completeness Check
- [ ] Does every topic from the routing table have a corresponding file?
- [ ] Are there topics mentioned in content but missing their own section?
- [ ] Does the index.md accurately summarize all topic files?

### Accuracy Spot-Check
- [ ] Verify 5-10 specific factual claims (rates, dates, holdings, quotes)
- [ ] Check that attributed positions actually match the named entity's public stance
- [ ] Confirm court case citations (court, year, judge, outcome)

### Freshness Check
- [ ] Is any content already outdated at time of creation?
- [ ] Are recent developments (last 6 months) adequately covered?
- [ ] Is the `learned/` layer set up to capture ongoing changes?

### Structural Check
- [ ] Every file has YAML metadata header
- [ ] Every major section has Key Takeaways block
- [ ] Cross-references between files are valid (filenames exist)
- [ ] Routing table maps all topics to files
- [ ] Index.md loads and answers common questions without needing deep-dives

---

## SECTION 5: MAINTENANCE PROTOCOL

### Ongoing Updates
- During sessions: agent appends to `learned/` files
- Weekly/monthly: human reviews `learned/`, promotes to curated files
- Quarterly: re-audit for consistency, completeness, freshness

### When to Rebuild vs. Patch
- **Patch** (update learned/, fix specific files): when <20% of content is affected
- **Partial rebuild** (regenerate specific topic files): when a major development
  changes an entire topic area
- **Full rebuild** (re-run generation pipeline): when >50% is stale, or when
  fundamental scope change occurs

### Version History
Track major KB versions in AGENT.md:
```
## KB Version History
- v1.0 (2026-03-25): Initial creation via multi-model pipeline
- v1.1 (2026-04-15): Updated US policy section for new legislation
- v2.0 (2026-09-01): Full rebuild incorporating 6 months of learned/ content
```
