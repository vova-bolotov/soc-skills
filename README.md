# CrowdStrike Fusion Workflows

A [Claude Code plugin](https://docs.anthropic.com/en/docs/claude-code/skills) for creating, validating, and deploying CrowdStrike Falcon Fusion SOAR workflows.

## Features

- Discover actions and triggers via the live CrowdStrike API
- Author workflows in YAML with correct schema and data references
- Handle CEL expressions, loop/conditional patterns
- Validate workflows before deployment
- Deploy via CI/CD pipeline with AWS Secrets Manager

## Getting Started

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview) CLI installed
- For CI/CD deployment: AWS account with Secrets Manager and GitHub Actions OIDC configured

### Installation

**Via Marketplace:**

This plugin can be installed from a Claude Code marketplace that includes it.

**Manual Installation:**

```bash
git clone https://github.com/eth0izzle/security-skills.git
cd security-skills
```

Then start Claude Code in the directory:

```bash
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
├── workflows/                    # Production workflows (deployed via CI/CD)
├── examples/fusion-workflows/    # Reference examples (not deployed)
└── .github/workflows/            # CI/CD pipeline
```

## CI/CD Deployment

Workflows in the `workflows/` directory are automatically deployed to CrowdStrike via GitHub Actions.

### Pipeline Flow

| Event | Action |
|-------|--------|
| PR to `main` | Validates workflows against CrowdStrike API |
| Merge to `main` | Deploys workflows to CrowdStrike |

### Credentials

Credentials are stored in **AWS Secrets Manager** (not in the repository):

```json
{
  "CS_CLIENT_ID": "your-client-id",
  "CS_CLIENT_SECRET": "your-client-secret",
  "CS_BASE_URL": "https://api.crowdstrike.com"
}
```

The pipeline uses OIDC to authenticate to AWS — no credentials are stored in GitHub.

### Setup Requirements

1. Create IAM role `github-actions-<repo-name>` with SecretsManager read access
2. Create secret `crowdstrike/fusion-api` in AWS Secrets Manager
3. Update `AWS_ACCOUNT_ID` in `.github/workflows/deploy-workflows.yaml`

See [SKILL.md](skills/fusion-workflows/SKILL.md) for detailed setup instructions.

## Skill Contents

| Directory | Contents |
|-----------|----------|
| `scripts/` | CLI tools: `action_search.py`, `validate.py`, `import_workflow.py`, `execute.py`, `export.py` |
| `references/` | `yaml-schema.md`, `cel-expressions.md`, `trigger-types.md`, `best-practices.md` |
| `assets/` | YAML templates for common workflow patterns |

## License

MIT
