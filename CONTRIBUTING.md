# Contributing a workflow

This is the contribution surface for [agenticpatterns.io](https://agenticpatterns.io). Every `.mdx` file you add under [`workflows/`](./workflows) becomes a card on the site within ~2 minutes of merge.

## TL;DR

1. Fork this repo
2. Add a new file at `workflows/<slug>.mdx` (slug = kebab-case, e.g. `my-workflow-name`)
3. Fill in the frontmatter (see template below). Add an MDX body **only** if you're writing an editorial-mode card
4. Open a PR
5. The validator Action runs automatically — if it fails, fix the reported issues and push again
6. Maintainer reviews + merges. Your workflow is live on the site in <2 min

## Field-by-field template

Start from one of the two templates below depending on which **mode** fits your workflow.

### Most common: Reference mode (vendor product, maintained docs)

Use this when there's an authoritative external doc you'd just point readers to (e.g., a vendor docs site, a maintained README, an SDK reference). The card renders the setup command + a link list pointing at the canonical docs.

```yaml
---
title: My Tool — My Workflow Name
tagline: One sentence describing what this workflow does and when it shines.
attribution: Vendor or Author Name
tier: tool               # or: snippet | framework
canonical_url: https://example.com/docs/my-workflow
setup_command: |
  # The actual install + run commands a user copies
  pip install my-tool
  my-tool init
when_to_use: One sentence on when this workflow is the right choice.
when_not_to_use: One sentence on when to avoid it.
tags: [my-tool, my-category, my-tag]
sources:
  - title: My Tool — official docs
    url: https://example.com/docs/my-workflow
    year: 2025
related:
  patterns: []           # slugs of patterns this workflow implements
  practices: []          # operational practices invoked
  tools: [my-tool-slug]  # if there's a tool entry, link it
  workflows: []          # sibling workflows
---
```

Body: **leave empty**. The site renders the setup command + reference list automatically.

### Editorial mode (no maintained canonical, or pure concept)

Use this when there's no maintained upstream doc you can trust (single-author blog, no LICENSE, AGPL, or a pure architectural concept). You write a ~200-word body describing the technique.

```yaml
---
title: Workflow Name
tagline: One sentence.
attribution: Originator (with date)
tier: framework          # usually framework or snippet for editorial cards
canonical_url: https://blog-post-or-repo-where-it-was-coined.example
when_to_use: One sentence.
when_not_to_use: One sentence.
tags: [tag1, tag2]
sources:
  - title: Original blog post
    author: Originator
    url: https://blog.example/post
    year: 2025
related:
  patterns: []
  practices: []
  workflows: []
---

A concrete first sentence explaining what the workflow IS mechanically. Coined by [Originator] in [date]. Adopted by [tools/teams].

The recipe inline as a fenced code block:

```bash
the actual command or pseudocode
```

A paragraph or short list explaining how it works — files, state, the loop, the gates.

**Use for** [one sentence on the sweet spot].

**Avoid in** [one sentence on where it fails].

Notes on variants or successors if relevant.

Canonical: https://...
```

## Editorial body — voice rules

When you're writing a body (editorial mode only — Reference cards leave the body empty), the rules are stricter than typical README prose because every card is a copy-paste-into-LLM artifact.

- **Open with the mechanism, not marketing.** First sentence: what it IS in mechanical terms.
- **Include the recipe inline as a code block.** Bash, shell command, pseudocode — the functional content is the high-value part.
- **Quote the originator at most once, with attribution.** Short, fair-use quote. Don't paraphrase paragraphs.
- **Bold inline "Use for" / "Avoid in".** Compact restatement of the frontmatter fields, useful in the LLM payload.
- **Note lineage if relevant.** Variants, successors, or predecessors.
- **End with `Canonical: <URL>`.** Even though it's in frontmatter, having it in the body means it appears in the `.md` payload too.

## What gets validated

The Action that runs on your PR (`.github/workflows/validate.yml`) checks:

- ✅ Required fields present: `title`, `tagline`, `tier`, `canonical_url`
- ✅ `tier` is one of `snippet | framework | tool`
- ✅ `canonical_url` is a valid HTTPS URL
- ✅ Slug (filename minus `.mdx`) matches `[a-z0-9-]+` and is unique
- ✅ `related` keys are subset of allowed collection names
- ✅ `sources` is an array, each with a `url`
- ✅ Frontmatter parses as valid YAML

A failed validation comments on your PR with what's wrong + where. Fix and push again.

## What makes a good workflow

Beyond the schema, here's what we look for in editorial review:

- **Distinct from existing entries.** Browse [`workflows/`](./workflows) first. If yours is a variant of an existing one, consider editing the existing entry instead.
- **A clear recipe.** The setup command should be something a reader can actually run. Vague handwaves get rejected.
- **Attribution.** Always credit the originator. Don't take credit for community techniques.
- **Specific when/avoid.** "Use for greenfield prototypes" beats "use for general coding."
- **2025–2026 sources.** The field moves fast; entries older than ~12 months get rotated unless they're foundational.

## Local preview

```bash
git clone https://github.com/coderabbitai/agentic-patterns.git
cd agentic-patterns
npm install
npm run lint                  # runs the validator on your changes
```

To preview rendering on the live site, you'll need to clone the main site repo (not public) — most contributors skip this and trust the validator.

## Questions

Open an issue at [github.com/coderabbitai/agentic-patterns/issues](https://github.com/coderabbitai/agentic-patterns/issues).
