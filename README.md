# Security Skills for Claude Code

A plugin marketplace of [Claude Code skills](https://docs.anthropic.com/en/docs/claude-code/skills) that automate security operations.

Install individual skills to automate specific platforms, or combine several for end-to-end integration across your security stack.

## Available Skills

| Skill | Platform | What It Does |
|-------|----------|--------------|
| [fusion-workflows](plugins/fusion-workflows/skills/fusion-workflows/) | CrowdStrike Falcon Fusion SOAR | Create, validate, import, execute, and export Fusion SOAR workflows. Discovers actions via the live API, authors YAML with correct schema and data references, handles CEL expressions, loop/conditional patterns, and manages the full workflow lifecycle. |

*More skills coming soon.*

## Getting Started

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview) (or Skills compatible) CLI installed
- For CI/CD deployment: AWS account with Secrets Manager and GitHub Actions OIDC configured

### Install via Plugin Marketplace

```bash
/plugin marketplace add https://github.com/eth0izzle/security-skills.git
/plugin install SKILL-NAME@security-skills
```

Replacing `SKILL-NAME` with the desired skill name you want to install.

### Manual Setup

If you prefer to install manually:

1. Clone the repository:

```bash
git clone https://github.com/eth0izzle/security-skills.git
cd security-skills
cp -r plugins/ ~/.claude/plugins/
```

2. Start Claude Code in the project directory:

```bash
claude
```

3. Ask Claude to build something:

```
/plan
> Create a workflow that contains a device and sends a Slack notification
> Create multiple workflows based on the attached BEC Playbook
> What CrowdStrike actions are available to help with forensics capture?
```

Claude will automatically use the appropriate skill based on your request.

## Repository Structure

```
soc-skills/
├── workflows/                    # Production workflows (deployed via CI/CD)
├── examples/fusion-workflows/    # Reference examples (not deployed)
├── plugins/                      # Claude Code skill definitions
│   └── fusion-workflows/
│       └── skills/fusion-workflows/
│           ├── SKILL.md          # Skill definition
│           ├── scripts/          # CLI tools for CrowdStrike API
│           ├── references/       # Schema docs, best practices
│           └── assets/           # YAML templates
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

See [SKILL.md](plugins/fusion-workflows/skills/fusion-workflows/SKILL.md) for detailed setup instructions.

## Using Skills Directly

Each skill lives under `plugins/<plugin-name>/skills/<skill-name>/` and includes:

- `SKILL.md` — the skill definition that Claude loads automatically
- `scripts/` — CLI tools for interacting with the platform API
- `references/` — schema docs, expression syntax, best practices
- `assets/` — templates and starter files

## Contributing

To add a new security skill:

1. Create a plugin directory under `plugins/<plugin-name>/`
2. Add a `.claude-plugin/plugin.json` manifest
3. Create the skill under `plugins/<plugin-name>/skills/<skill-name>/`
4. Write a `SKILL.md` that describes the skill's capabilities, prerequisites, and step-by-step workflow
5. Add scripts for API interaction, validation, and deployment
6. Add reference docs for schema, syntax, and best practices
7. Add template assets for common patterns
8. Register the plugin in `.claude-plugin/marketplace.json`
9. Submit a pull request

See the [fusion-workflows skill](plugins/fusion-workflows/skills/fusion-workflows/SKILL.md) as a reference implementation.

## License

MIT
