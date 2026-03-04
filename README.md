# AI Product Toolkit — Claude Code Plugin Marketplace

> A **Claude Code Plugin Marketplace** that distributes product skills to any project, plus an archive of AI-generated PRDs.

## Why I Built This

As a technical PM at an EdTech startup, I kept hitting the same friction: writing PRDs from scratch was slow, experiments launched without a trace, and ICE scores were guessed rather than evidence-based. So I automated my product workflow into Claude Code skills — forcing rigor (additive Confidence Meter, kill criteria, experiment lifecycle) without killing velocity. The result: a 30-second `/prd-quick` captures chaos, `/prd` runs an interactive ICE calibration, and `/prd-retro` reconstructs documentation from code that already shipped.

This is a real tool I use daily, not a demo project.

**Dorian Erkens** — Product Manager & Builder | [LinkedIn](https://www.linkedin.com/in/dorianerkens/)

## What This Is

This repo serves two purposes:

1. **Plugin Marketplace** — distributes product management skills (`/prd`, `/prd-quick`, `/prd-retro`, `/experiment-new`, `/feedback-to-idea`) to any team member, in any repository
2. **PRD Archive** — centralized storage for AI-generated PRDs with real-world examples (anonymized)

## Installation

```bash
# 1. Add the marketplace
/plugin marketplace add DorianErkens/AI-Product-Claude-marketplace

# 2. Install the product-toolkit plugin
/plugin install product-toolkit@product-toolkit-marketplace
```

The 5 skills are then available in any project.

## Skills

| Skill | Usage | What It Does |
|-------|-------|-------------|
| `/prd` | `/prd "idea + context"` | Full structured PRD — GIST framework, interactive ICE scoring, Confidence Meter, Strategic Alignment |
| `/prd-quick` | `/prd-quick "quick idea"` | 30-second minimal PRD — captures chaos, zero questions, assumptions marked |
| `/prd-retro` | `/prd-retro feat/my-branch` | Product archaeology — reconstructs a PRD from existing code/branch |
| `/experiment-new` | `/experiment-new prd/my-prd.md` | Adds an Experiment Register to a PRD — tier, kill criteria, metrics, decision log |
| `/feedback-to-idea` | `/feedback-to-idea <feedback-url> <objective-url>` | Transforms a project management feedback into a structured Idea (GIST) with interactive ICE, creates the task as a subtask of the objective, and adds traceability comments |

### Typical Workflow

```
/prd-quick "push notifs for teachers"     → capture the idea in 30s
/prd "absence stats dashboard"            → structured PRD with interactive ICE
/experiment-new prd/my-prd.md             → append Experiment Register
/prd-retro feat/chatbot-v2                → reconstruct PRD from existing code
/feedback-to-idea <fb-url> <obj-url>     → feedback → Idea GIST + ICE + traceability
```

### Frameworks & Sources

These skills are built on established product management frameworks:

| Framework | Author | Used In | Source |
|-----------|--------|---------|--------|
| **GIST** (Goals, Ideas, Steps, Tasks) | Itamar Gilad | `/prd`, `/prd-retro` | [Why I Stopped Using Product Roadmaps](https://itamargilad.com/why-i-stopped-using-product-roadmaps/) |
| **ICE Scoring** (Impact, Confidence, Effort) | Sean Ellis, adapted by Itamar Gilad | All skills | [The Tool I Use to Evaluate 200 Product Ideas](https://itamargilad.com/the-tool-i-use-to-evaluate-200-product-ideas/) |
| **Confidence Meter** (additive evidence-based scoring) | Itamar Gilad | `/prd`, `/prd-quick`, `/experiment-new` | [I've Stopped Using ICE / RICE — Here's What I Use Instead](https://itamargilad.com/confidence-meter/) |
| **Experiment Register** (lifecycle: Hypothesis → Prototyping → Measuring → Decision) | Inspired by Itamar Gilad's "Testing Business Ideas" approach | `/experiment-new` | [Evidence-Guided](https://itamargilad.com/book-erta/) |
| **Vibe Coding PRD** (chaos-first, assumption-tagged) | Original design | `/prd-quick`, `/prd-retro` | — |
| **Feedback-to-Idea pipeline** (feedback → GIST Idea + ICE + traceability) | Original design | `/feedback-to-idea` | — |

Key design decisions:
- **Confidence is additive, not estimated** — you sum evidence scores rather than guessing a number. This forces teams to collect real evidence before claiming confidence.
- **ICE uses I × C / E** (not I × C × E) — Effort is in the denominator because higher effort should reduce priority, not increase it.
- **Assumptions are first-class citizens** — every unknown is explicitly tagged `(Assumption)` so it can be validated or challenged later.
- **/prd-retro exists because most experiments start before a PRD** — this skill traces the chaos after the fact, which is more realistic than expecting upfront documentation.
- **/feedback-to-idea closes the feedback loop** — customer feedback becomes a structured Idea (subtask of a Goal) with full ICE scoring and traceability comments on all linked tasks. No feedback gets lost in a backlog.

## Architecture

```
AI-Product-Claude-marketplace/
│
├── .claude-plugin/
│   └── marketplace.json                ← marketplace catalog (points to plugins/)
│
├── plugins/                            ← DISTRIBUTED PLUGINS
│   └── product-toolkit/                  ← "product-toolkit" plugin
│       ├── .claude-plugin/
│       │   └── plugin.json               ← manifest (name, version, description)
│       └── skills/                       ← the 5 product skills
│           ├── prd/SKILL.md
│           ├── prd-quick/SKILL.md
│           ├── prd-retro/SKILL.md
│           ├── experiment-new/SKILL.md
│           └── feedback-to-idea/SKILL.md
│
├── prd/                                ← PRD ARCHIVE (3 anonymized examples)
│   ├── example-chatbot-kb-scalability.md
│   ├── example-ocr-paper-attendance.md
│   └── example-bulk-justification.md
│
├── prompts/                            ← reusable prompts
├── prototypes/                         ← experimental code, POCs
└── notes/                              ← session notes, learnings
```

### Quick Navigation

| I want to... | Go to... |
|-------------|----------|
| Edit a product skill | `plugins/product-toolkit/skills/<skill>/SKILL.md` |
| Add a new plugin | `plugins/<name>/.claude-plugin/plugin.json` + `plugins/<name>/skills/` |
| Read/add a PRD | `prd/` |
| See the marketplace catalog | `.claude-plugin/marketplace.json` |

## MCP Adaptability

The `/feedback-to-idea` skill uses ClickUp as its project management backend via the ClickUp MCP server. However, the workflow is **tool-agnostic by design** — the GIST framework, ICE scoring, and Confidence Meter are universal product management patterns.

To adapt this to your own stack, swap the ClickUp MCP tools for your project management tool's MCP server:

| Current (ClickUp) | Alternative |
|-------------------|-------------|
| `clickup_get_task` | Notion MCP `notion_get_page`, Jira MCP `jira_get_issue`, Linear MCP `linear_get_issue` |
| `clickup_create_task` | `notion_create_page`, `jira_create_issue`, `linear_create_issue` |
| `clickup_create_task_comment` | `notion_add_comment`, `jira_add_comment`, `linear_create_comment` |

The skill's SKILL.md references ClickUp tool names — replace them with the equivalent MCP tool names for your platform. The rest of the workflow (ICE scoring, PRD generation, traceability comments) stays identical.

## Adding Your Own Skills

1. Create a new skill directory under `plugins/product-toolkit/skills/<name>/`
2. Add a `SKILL.md` with YAML frontmatter (`name`, `description`, `argument-hint`, `allowed-tools`)
3. Update `marketplace.json` if adding a new plugin (not needed for skills within existing plugins)

## Conventions

- Name PRD files with dates: `YYYY-MM-DD_topic-name.md`
- Use subdirectories in `prd/` for complex features
- Skills follow Claude Code format: `skills/<name>/SKILL.md` with YAML frontmatter

## License

MIT
