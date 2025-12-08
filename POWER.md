---
name: "bluebricks"
displayName: "Bluebricks Environment Orchestration"
description: "One-click environment orchestration - deploy production-ready cloud infrastructure with AI-powered blueprints and IaC guardrails"
keywords: ["infrastructure", "blueprints", "terraform", "helm", "deployment", "iac", "bluebricks", "cloud", "devops", "environments"]
author: "Bluebricks"
---

# Bluebricks Environment Orchestration

## Overview

Stop chasing infrastructure. Start delivering environments.

Bluebricks transforms your Infrastructure-as-Code into reusable, versioned blueprints that AI agents can deploy with confidence. Provision complex production environments in one click, standardize across clouds and teams, and let AI handle infrastructure with deterministic guardrails.

**Key capabilities:**
- **Blueprint Discovery**: Find and deploy reusable infrastructure blueprints instantly
- **One-Click Environments**: Provision Dev, Staging, and Production with consistent configuration
- **AI-Ready Orchestration**: Give agents the context they need for safe, standards-based deployments
- **Multi-IaC Support**: Works with Terraform, OpenTofu, Helm, CloudFormation, and Bicep
- **Governed Approvals**: Review plans before applying—your IaC as both blueprint and guardrail

## Available Steering Files

- **getting-started** - Get your first environment running in minutes
- **blueprint-discovery** - Search, filter, and understand blueprints
- **deployment-workflows** - Patterns for updates, promotions, and monitoring

## Available MCP Servers

### bluebricks

**Binary:** `bricks` CLI  
**Connection:** stdio

| Tool | Purpose |
|------|---------|
| `bricks-list-blueprints` | Search blueprints with fuzzy matching and filtering |
| `bricks-list-environments` | List environments with deployment context |
| `bricks-list-deployments` | List deployments with status filtering |
| `bricks-install` | Deploy blueprints or update existing deployments |
| `bricks-deployment-approve` | Approve pending deployment plans |
| `bricks-deployment-reject` | Reject deployment plans |
| `bricks-deployment-status` | Check real-time deployment status |

## When to Use This Power

- Deploying cloud infrastructure with reusable blueprints
- Setting up Dev, Staging, and Production environments consistently
- Managing infrastructure deployments across AWS, Azure, or GCP
- Reviewing and approving infrastructure changes before apply
- Searching for blueprints (Terraform, Helm, CloudFormation, Bicep)

## Getting Started

**For new users:** Use the getting-started steering file for complete step-by-step guidance. Access it with `readPowerSteering("bluebricks", "getting-started.md")`.

**For blueprint discovery:** Use `readPowerSteering("bluebricks", "blueprint-discovery.md")` to search, filter, and understand blueprint properties.

**For deployment workflows:** Use `readPowerSteering("bluebricks", "deployment-workflows.md")` for patterns on updates, promotions, and monitoring.

## Using MCP Tools

### Search Blueprints

```
usePower("bluebricks", "bluebricks", "bricks-list-blueprints", {
  "query": "postgres",
  "include_properties": true
})
```

### List Environments

```
usePower("bluebricks", "bluebricks", "bricks-list-environments", {})
```

### Deploy Infrastructure

```
usePower("bluebricks", "bluebricks", "bricks-install", {
  "blueprint": "aws_rds_postgres",
  "environment": "staging",
  "props": "{\"instance_class\": \"db.t3.medium\"}",
  "wait-for-approval": true
})
```

### Approve Deployment

```
usePower("bluebricks", "bluebricks", "bricks-deployment-approve", {
  "deployment": "my-postgres-staging",
  "task": "7c9e6679-7425-40de-944b-e07fc1f90ae7"
})
```

## Best Practices

**Do:**
- Review plans before approving—your guardrails matter
- Use environment properties for consistent configuration
- Reference secrets with `Secrets.*`—never hardcode credentials
- Test in Dev/Staging before Production

**Don't:**
- Skip plan review—always understand what's changing
- Use `apply: true` in production—prefer governed approvals
- Put sensitive data in props—use `Secrets.*` references

## Configuration

**Prerequisites:** Bricks CLI installed (`brew install bluebricks/tap/bricks` or [download](https://bluebricks.co/docs/getting-started/installation))

1. Install the Bricks CLI
2. Authenticate: `bricks login`

**MCP Configuration:**
```json
{
  "mcpServers": {
    "bluebricks": {
      "command": "bricks",
      "args": ["mcp"],
      "disabled": false,
      "autoApprove": [
        "bricks-list-blueprints",
        "bricks-list-environments",
        "bricks-list-deployments"
      ]
    }
  }
}
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `bricks: command not found` | Install CLI: `brew install bluebricks/tap/bricks` |
| Authentication failed | Run `bricks login` to authenticate |
| Blueprint not found | Use `bricks-list-blueprints` with `query` to search |
| Props format error | Use valid JSON string: `"{\"key\": \"value\"}"` |

## Resources

- [Bluebricks Documentation](https://bluebricks.co/docs)
- [Bluebricks MCP Guide](https://bluebricks.co/docs/agentic-ai/bricks-mcp)
- [Bluebricks Platform](https://app.bluebricks.co)
- [API Reference](https://docs.bluebricks.co/bluebricks-documentation/api/overview)

---

**Binary:** `bricks` CLI  
**Connection:** stdio
