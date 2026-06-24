# Workflow frontmatter schema

Every `.mdx` file under `workflows/` has YAML frontmatter followed by an optional MDX body. This document is the authoritative reference for what fields are valid.

## Required fields

| Field | Type | Notes |
|---|---|---|
| `title` | string | Title-case, e.g. `OpenAI Agents SDK - Handoffs` |
| `tagline` | string | One sentence, ≤180 chars, no trailing period preferred |
| `tier` | enum | One of: `snippet`, `framework`, `tool` (see semantics below) |
| `canonical_url` | URL | The official source for this workflow (vendor docs, blog, repo) |

## Optional fields

| Field | Type | Notes |
|---|---|---|
| `attribution` | string \| null | Originator (person, vendor, or org). Defaults to null. |
| `tags` | string[] | 1–5 descriptive labels: `loop`, `multi-agent`, `claude-code`, etc. |
| `setup_command` | string (block) | The install + run incantation. Use `\|` for multi-line YAML. |
| `when_to_use` | string | One sentence: when this workflow is the right choice |
| `when_not_to_use` | string | One sentence: when to avoid it |
| `upstream` | object | GitHub repo info for license-gated mirroring (see below) |
| `requires` | string | Runtime requirement (paid product, IDE extension, etc.). Surfaced in the detail-page meta block. |
| `autonomous` | boolean | Default `false`. Set `true` for workflows that run an agent unattended. Triggers a safety banner above the setup command. |
| `use_cases` | string[] | Slugs from the use-cases collection on the main site. Drives §4 of the use-case landing pages. Valid values: `building-features`, `code-review`, `test-generation`, `bug-fixing`, `refactoring`, `migrations`, `dependency-upgrades`, `security-remediation`, `documentation`, `release-readiness`, `spec-authoring`. |
| `inputs` | string[] | 5-7 concrete items the agent needs as inputs (e.g., "the failing test output", "the repo's AGENTS.md"). Surfaced when a workflow is referenced from a use-case page. |
| `review_gate` | object | The workflow's answer to The Review Gate's three questions (trust / standards / merge). See schema below. Renders a compact rubric on the detail page. |
| `checkpoints` | object[] | Human-in-the-loop pause points. Empty array (`[]`) is a meaningful signal: no gates. See schema below. |
| `sources` | source[] | References (see schema below) |
| `related` | related | Cross-links into the site's knowledge base (see schema below) |
| `draft` | boolean | Default `false`. Set `true` to hide from the site without deleting. |

## `upstream` shape

Optional. When present, the build pipeline detects the upstream license and decides whether to mirror the named files or just reference-link them.

```yaml
upstream:
  repo: owner/repo                     # e.g. "openai/openai-agents-python"
  ref: main                            # branch, tag, or commit SHA. Default "main".
  paths:                               # files to mirror (empty array = reference-only)
    - README.md
    - templates/commands/specify.md
```

**License gate** (applied at build time, no contributor decision needed):

| Upstream LICENSE detected | Behavior |
|---|---|
| MIT / Apache-2.0 / BSD / MPL-2.0 / CC0 | **Mirror** — full content embedded in card + payload |
| AGPL / GPL / Proprietary / no LICENSE | **Reference** — list URLs, do not embed |

## `sources` shape

```yaml
sources:
  - title: Page or post title         # optional
    author: Author Name               # optional
    url: https://...                  # required
    year: 2025                        # optional
```

At least one source is encouraged. Sourcing is the cornerstone of the catalog's credibility.

## `related` shape

Slug references into the site's knowledge base. The slug is the entry's filename (without `.mdx`) in the corresponding directory of the main site.

```yaml
related:
  laws: []                            # slugs of laws
  patterns: []                        # slugs of patterns
  antiPatterns: []                    # slugs of anti-patterns
  practices: []                       # slugs of best practices
  glossary: []                        # slugs of glossary terms
  tools: []                           # slugs of tools
  aiAssistants: []                    # slugs of AI assistants
  workflows: []                       # slugs of sibling workflows
```

**Note:** the slugs must exist on the main site. If you're unsure what slugs are available, check the live site at [agenticpatterns.io](https://agenticpatterns.io) — each detail page URL ends with its slug.

Don't worry about being exhaustive. 3–6 strong links across 2–3 sections is better than 30 weak ones. The validator caps at 3 per section; extras are dropped.

## `tier` semantics

| Tier | Means | Typical example |
|---|---|---|
| `snippet` | Small paste-able pattern. No install, no scaffolding. | Reflexion loop, Plan-approval gate, Review-agent-on-every-merge |
| `framework` | Needs scaffolding (files / skills / CLI) installed into your agent. | Ralph Wiggum loop, PIV Loop, Spec Kit |
| `tool` | Commits to a specific tool or product (open- or closed-source). | Cline Plan & Act, OpenAI Handoffs, Cursor BG agents |

When in doubt, pick `tool` — it's the most common.

## `review_gate` shape

Optional. When present, renders a compact rubric on the detail page showing how this workflow answers the three questions of [The Review Gate](https://agenticpatterns.io/the-review-gate). Non-tool answers (CI, lint, human review, type-system) are first-class options — "tool-assisted" is one option among five.

```yaml
review_gate:
  trust: tool-assisted | type-system | human-only | ci-tests | not-addressed
  standards: lint-typecheck | tool-assisted | custom-rules | human-only | not-addressed
  merge: auto-ci | human-gate | plan-approval | tool-assisted | not-addressed
  description: "1-line gloss of what's gated and how"
```

## `checkpoints` shape

Optional. List of human-in-the-loop pause points. Empty array (`[]`) means the workflow runs without gates.

```yaml
checkpoints:
  - phase: before-implementation
    description: "Human approves the plan before code is written"
  - phase: before-merge
    description: "Reviewer signs off after automated checks pass"
```

## MDX body

**Reference-mode and Mirror-mode cards:** leave the body empty. The site renderer assembles the card from frontmatter + (optional) mirrored upstream artifacts.

**Editorial-mode cards:** write ~200 words of prose. See [CONTRIBUTING.md](./CONTRIBUTING.md#editorial-body--voice-rules) for the voice rules.

## Slug rules

- Filename: `kebab-case-slug.mdx`
- Allowed characters: `[a-z0-9-]+`
- Maximum length: 60 characters
- Must be unique across `workflows/`
- Should NOT collide with slugs already used on the main site for `patterns/` or `glossary/`. If a pattern by the same name exists on the site, suffix your workflow with `-loop`, `-plugin`, `-cycle`, etc. (e.g., `evaluator-optimizer-loop` because `evaluator-optimizer` is a pattern slug).

## Full example: minimum-viable Reference card

```yaml
---
title: My Vendor - My Workflow
tagline: One-sentence pitch describing what the workflow does.
attribution: My Vendor
tier: tool
canonical_url: https://my-vendor.com/docs/my-workflow
setup_command: |
  npm install -g my-vendor-cli
  my-vendor init
when_to_use: When you need X under condition Y.
when_not_to_use: When Z is true.
tags: [my-vendor, multi-agent]
use_cases: [building-features, bug-fixing]
sources:
  - title: My Vendor - official docs
    url: https://my-vendor.com/docs/my-workflow
    year: 2025
---
```

12 lines of frontmatter, no body. Fully valid.

## `loop` (optional)

Present when the workflow is a **loop** — it iterates against a feedback gate
until an exit condition. Its presence marks the workflow as a loop (badge +
catalog filter) and its fields render a generated loop diagram.

```yaml
loop:
  trigger: "What starts the loop"
  steps:                      # the loop body, top to bottom
    - "Act"
  gate: "Feedback gate question?"
  exit: "Exit condition / terminal"
  back: "not done"            # optional loop-back edge label (default: "not done")
```
