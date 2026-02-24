---
name: enzyme
description: >
  Explore an Obsidian vault using Enzyme — surface connections between ideas,
  find latent patterns across notes. Use when the user wants to explore their
  thinking, draw connections, or search their vault by concept rather than keyword.
allowed-tools: Bash, Read, Glob, Grep
---

# Enzyme — Vault Exploration Skill

## Vault Location

The Obsidian vault lives at `/workspace/group/vault/`.

**All enzyme commands must be run from the vault directory:**

```bash
cd /workspace/group/vault && enzyme petri
cd /workspace/group/vault && enzyme catalyze "query here"
```

If `/workspace/group/vault/` doesn't exist, the vault may not be mounted for this group. Check with `ls /workspace/group/` to see what's available.

## What Enzyme Is

Enzyme turns your Obsidian vault into something you can converse with. It works through three concepts:

**Entities** are the tags (`#travel`), wikilinks (`[[open questions]]`), and folders (`/people`) in your vault. Each one is a semantic cluster — a gathering of content you've already organized by how you think. Hierarchical tags like `#travel/pyrenees` create nested clusters.

**Catalysts** are AI-generated questions anchored to each entity. They probe what's latent in that cluster. A catalyst for `#travel` might be: *"What kept pulling you forward when something was asking you to stay?"* — and content surfaces because it **speaks to the question**, not because it contains matching words. The same entity explored through different catalysts reveals different material.

**Petri** is the live readout of what's growing in your vault — which entities are active, what catalysts have formed around them, where the thinking is heading. Each entity carries temporal metadata: when you last engaged it, how frequently, whether it's active or dormant. Dormant entities are often the most interesting — they surface threads you've stopped noticing.

Content retrieval works by **resonance with catalyst questions**, not keyword matching. The catalysts encode the vault's own vocabulary for its themes — they're handles the vault has grown. Reaching for them connects you to content that generic search terms won't find.

## Prerequisites

Before running enzyme commands, check that the vault is initialized:

```bash
# Check for .enzyme directory in the vault
ls /workspace/group/vault/.enzyme/enzyme.db
```

- If `.enzyme/enzyme.db` exists: vault is ready. Run `enzyme petri` from the vault directory.
- If it doesn't exist: run `cd /workspace/group/vault && enzyme init` to initialize the vault.
- Ensure `enzyme` is on PATH (virtual environment activated or installed globally).

## Commands

### `enzyme petri` — See what's growing

Returns JSON with trending entities and their catalysts.

```bash
cd /workspace/group/vault && enzyme petri                          # Default: top 7 entities, 5 catalysts each
cd /workspace/group/vault && enzyme petri --top 10 -n 3           # Top 10 entities, 3 catalysts each
cd /workspace/group/vault && enzyme petri --no-guide              # Omit presentation_guide from output
cd /workspace/group/vault && enzyme petri --no-filter             # Return all entities without smart filtering
```

Key flags: `--top N` (default 7), `--catalysts-per-entity N` (default 5), `--no-guide`, `--no-filter`

### `enzyme catalyze "query"` — Search by concept

Activates the vault's catalysts to surface resonant content. Returns JSON with matched excerpts, file paths, and contributing catalysts.

```bash
cd /workspace/group/vault && enzyme catalyze "feeling stuck"
cd /workspace/group/vault && enzyme catalyze "tension between efficiency and presence" --max-catalysts 6
cd /workspace/group/vault && enzyme catalyze "cost of care" --limit 10 --threshold 0.2
```

Key flags: `--max-catalysts N`, `--limit N`, `--threshold FLOAT` (default 0.1), `--format json`

### `enzyme init [path]` — Initialize a vault

```bash
cd /workspace/group/vault && enzyme init                           # Initialize current directory
enzyme init /workspace/group/vault                                 # Initialize by absolute path
enzyme init --guide VAULT_GUIDE.md                                 # Use a guide file to inform initialization
```

### `enzyme refresh` — Update catalysts

Run when vault content has changed significantly. Regenerates catalysts and embeddings.

```bash
cd /workspace/group/vault && enzyme refresh
```

### When to use `catalyze` vs `Grep`

**Use Grep when you have a concrete anchor** — something that exists verbatim in the vault:
- People: "Sarah", `[[Dr. Chen]]`
- Tags: `#productivity`, `#enzyme/pmf`
- Links/titles: `[[On Writing Well]]`, `[[meeting notes]]`
- Files: `Readwise/Articles/...`, book titles, paper names
- Proper nouns: places, companies, projects

**Use `catalyze` when you only have a theme/concept** — no anchor to grep:
- "What have I written about feeling stuck?" (no name, no tag, no title)
- "cost of care in algorithmic interfaces" (academic framing — vault won't use these words)
- "tension between efficiency and presence" (conceptual, not anchored)

The test: would these exact words appear in their notes? Names and tags always do. Abstract/academic language rarely does — vaults use personal, concrete phrasing.

## Workflow

1. **Start with petri.** Run `enzyme petri` from the vault directory to see the landscape — what's active, what's dormant, what catalysts have formed. Present findings following [petri-guide.md](petri-guide.md).

2. **Ground in evidence.** Before making observations, use catalysts from the petri to run `enzyme catalyze` searches. Their words first, then you speak.

3. **Follow threads.** Use catalysts from petri results to drive searches based on what the user responds to. A catalyst for one entity often surfaces content connecting to another.

4. **Present search results** following [search-guide.md](search-guide.md). Lead with their words from matched excerpts, notice tensions across results, suggest specific next searches using catalyst language.

5. **If the vault isn't initialized** (no `.enzyme/enzyme.db`), tell the user and offer to run `enzyme init`. If results seem stale, suggest `enzyme refresh`.
