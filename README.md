# CrowdStrike Fusion Workflows

A [Claude Code plugin](https://docs.anthropic.com/en/docs/claude-code/skills) for creating and validating CrowdStrike Falcon Fusion SOAR workflows.

## Features

- Discover actions and triggers via the live CrowdStrike API
- Author workflows in YAML with correct schema and data references
- Handle CEL expressions, loop/conditional patterns
- Validate workflows before deployment

## Getting Started

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview) CLI installed

### Installation

**Via Marketplace:**

This plugin can be installed from a Claude Code marketplace that includes it.

**Manual Installation:**

```bash
git clone https://github.com/AutoScout24/soc-skills.git
cd soc-skills
claude
```

### Usage

Ask Claude to build workflows:

```
> Create a workflow that contains a device and sends a Slack notification
> Create multiple workflows based on the attached BEC Playbook
> What CrowdStrike actions are available to help with forensics capture?
```

Claude will automatically use the skill based on your request.

## Repository Structure

```
soc-skills/
├── .claude-plugin/plugin.json    # Plugin manifest
├── skills/fusion-workflows/      # Skill definition
│   ├── SKILL.md                  # Skill instructions for Claude
│   ├── scripts/                  # CLI tools for CrowdStrike API
│   ├── references/               # Schema docs, best practices
│   └── assets/                   # YAML templates
└── examples/fusion-workflows/    # Reference examples
```

## Deploying Workflows

Generated workflows should be committed to [soc-soar-workflows](https://github.com/AutoScout24/soc-soar-workflows) for CI/CD deployment to CrowdStrike.

## Skill Contents

| Directory | Contents |
|-----------|----------|
| `scripts/` | CLI tools: `action_search.py`, `validate.py`, `import_workflow.py`, `execute.py`, `export.py` |
| `references/` | `yaml-schema.md`, `cel-expressions.md`, `trigger-types.md`, `best-practices.md` |
| `assets/` | YAML templates for common workflow patterns |

## Local Development

Set credentials in your terminal session (not persisted):

```bash
export CS_CLIENT_ID="your-client-id"
export CS_CLIENT_SECRET="your-client-secret"
export CS_BASE_URL="https://api.crowdstrike.com"
```

Then use the scripts directly:

```bash
python skills/fusion-workflows/scripts/action_search.py --search "contain"
python skills/fusion-workflows/scripts/validate.py my-workflow.yaml
```
