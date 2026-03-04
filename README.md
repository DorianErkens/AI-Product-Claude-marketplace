# AI Product Toolkit — Claude Code Plugin Marketplace

> A **Claude Code Plugin Marketplace** that distributes product skills to any project, plus an archive of AI-generated PRDs.

## What This Is

This repo serves two purposes:

1. **Plugin Marketplace** — distributes product management skills (`/prd`, `/prd-quick`, `/prd-retro`, `/experiment-new`) to any team member, in any repository
2. **PRD Archive** — centralized storage for AI-generated PRDs, prompts, prototypes and notes

## Installation

```bash
# 1. Add the marketplace
/plugin marketplace add DorianErkens/AI-Product-Claude-marketplace

# 2. Install the product-toolkit plugin
/plugin install product-toolkit@product-toolkit-marketplace
```

The 4 skills are then available in any project.

## Skills

| Skill | Usage | What It Does |
|-------|-------|-------------|
| `/prd` | `/prd "idea + context"` | Full structured PRD — GIST framework, interactive ICE scoring, Confidence Meter, Strategic Alignment |
| `/prd-quick` | `/prd-quick "quick idea"` | 30-second minimal PRD — captures chaos, zero questions, assumptions marked |
| `/prd-retro` | `/prd-retro feat/my-branch` | Product archaeology — reconstructs a PRD from existing code/branch |
| `/experiment-new` | `/experiment-new prd/my-prd.md` | Adds an Experiment Register to a PRD — tier, kill criteria, metrics, decision log |

### Typical Workflow

```
/prd-quick "push notifs for teachers"     → capture the idea in 30s
/prd "absence stats dashboard"            → structured PRD with interactive ICE
/experiment-new prd/my-prd.md             → append Experiment Register
/prd-retro feat/chatbot-v2               → reconstruct PRD from existing code
```

### Frameworks Used

- **GIST** (Goals, Ideas, Steps, Tasks) — by Itamar Gilad
- **ICE Scoring** (Impact, Confidence, Effort) — with additive Confidence Meter
- **Experiment Register** — lifecycle tracking from Hypothesis → Prototyping → Measuring → Decision

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
│       └── skills/                       ← the 4 product skills
│           ├── prd/SKILL.md
│           ├── prd-quick/SKILL.md
│           ├── prd-retro/SKILL.md
│           └── experiment-new/SKILL.md
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
