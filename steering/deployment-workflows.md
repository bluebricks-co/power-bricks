# Deployment Workflows

## Overview

Advanced patterns for updating, promoting, and managing Bluebricks deployments.

> For first-time setup, see **getting-started**.

## Deployment States

```
PLANNING → PLANNED → INSTALLING → COMPLETED / NO_CHANGE / ERROR / CANCELED
```

| State | What's Happening |
|-------|------------------|
| PLANNING | Generating infrastructure plan |
| PLANNED | Awaiting your approval |
| INSTALLING | Applying changes to cloud |
| COMPLETED | Successfully deployed |
| NO_CHANGE | Already up to date |
| ERROR | Something went wrong |
| CANCELED | You rejected it |

---

## Updating Deployments

**Key insight:** Use `deployment` instead of `blueprint` to update existing infrastructure.

### Find Your Deployment

```javascript
// By environment
bricks-list-deployments({
  "environment": "staging",
  "blueprint": "aws_rds_postgres"
})

// By name
bricks-list-deployments({ "search": "postgres-staging", "fetchProps": true })
```

### Update Properties

```javascript
// Only send what changed—Bluebricks merges with existing config
bricks-install({
  "deployment": "my-postgres-staging",
  "props": "{\"instance_class\": \"db.t3.large\"}"
})
```

### Upgrade Blueprint Version

```javascript
bricks-install({
  "deployment": "my-postgres-staging",
  "blueprint": "aws_rds_postgres@2.0.0"
})
```

### Migrate Environment

```javascript
bricks-install({
  "deployment": "my-postgres-staging",
  "environment": "production",
  "props": "{\"instance_class\": \"db.t3.xlarge\"}"
})
```

---

## Properties Format

**Must be a JSON string:**

```javascript
// Correct - JSON string
"props": "{\"instance_class\": \"db.t3.medium\"}"

// Wrong - raw object
"props": {"instance_class": "db.t3.medium"}
```

### Property Types

| Type | Example |
|------|---------|
| Literal | `"instance_class": "db.t3.medium"` |
| Secret | `"password": "Secrets.db_password"` |
| Data Reference | `"vpc_id": "Data.vpc.vpc_id"` |
| Context | `"name": "${{bricks.deployment.slug}}"` |

### Auto-Injection

Environment properties inject automatically when names match:
- Environment has `region: us-east-1`
- Blueprint prop `region` gets filled in

---

## Using Environment Properties

**Best practice:** Let environments provide common configuration instead of hardcoding values.

### Discover Environment Props

```javascript
// See what properties an environment provides
bricks-list-environments({ "expand": true })
```

Example response:
```json
{
  "name": "staging",
  "properties": {
    "region": "us-east-1",
    "vpc_id": "vpc-abc123",
    "db_subnet_group": "staging-db-subnets",
    "kms_key_id": "arn:aws:kms:..."
  }
}
```

### Deploy Using Environment Props

**Don't repeat what the environment already knows:**

```javascript
// Avoid - hardcoding environment-level values
bricks-install({
  "blueprint": "aws_rds_postgres",
  "environment": "staging",
  "props": "{\"region\": \"us-east-1\", \"vpc_id\": \"vpc-abc123\", \"instance_class\": \"db.t3.medium\"}"
})

// Preferred - only deployment-specific values
bricks-install({
  "blueprint": "aws_rds_postgres",
  "environment": "staging",
  "props": "{\"instance_class\": \"db.t3.medium\"}"
})
```

### When to Override

Override environment props only when you need something different:

```javascript
// Use a different region than the environment default
bricks-install({
  "blueprint": "aws_rds_postgres",
  "environment": "staging",
  "props": "{\"region\": \"eu-west-1\", \"instance_class\": \"db.t3.medium\"}"
})
```

### Agent Guidance

When deploying, **always check environment props first**:

1. Run `bricks-list-environments({ "expand": true })` to see available properties
2. Only include props in your deployment that:
   - Are required by the blueprint but NOT provided by the environment
   - Need to override the environment default
3. Let the environment handle: region, VPC, subnets, KMS keys, common tags

---

## Common Patterns

### Environment Promotion

Same blueprint, different environments:

```javascript
// Dev → Staging → Production
bricks-install({ "blueprint": "my_stack@1.0.0", "environment": "dev" })
bricks-install({ "blueprint": "my_stack@1.0.0", "environment": "staging" })
bricks-install({ "blueprint": "my_stack@1.0.0", "environment": "production" })
```

### Version Upgrades

Test before promoting:

```javascript
// 1. Dev first
bricks-install({ "deployment": "my-app-dev", "blueprint": "my_stack@2.0.0" })

// 2. Then Staging
bricks-install({ "deployment": "my-app-staging", "blueprint": "my_stack@2.0.0" })

// 3. Finally Production
bricks-install({ "deployment": "my-app-production", "blueprint": "my_stack@2.0.0" })
```

### Preview Before Apply

```javascript
// See what would change
bricks-install({
  "deployment": "my-postgres-production",
  "props": "{\"instance_class\": \"db.t3.xlarge\"}",
  "plan-only": true
})
// Review plan_link, then run without plan-only
```

---

## Monitoring

### Check Status

```javascript
bricks-deployment-status({ "deployment": "my-postgres-staging" })
```

### Find by Status

```javascript
// Pending approvals
bricks-list-deployments({ "status": "PLANNED" })

// Failed
bricks-list-deployments({ "status": "ERROR" })

// In progress
bricks-list-deployments({ "status": "INSTALLING" })
```

### Full History

```javascript
bricks-list-deployments({ "environment": "staging", "all": true })
```

---

## Quick Reference

| Scenario | Tool | Parameters |
|----------|------|------------|
| Update deployment | `bricks-install` | `deployment`, `props` |
| Upgrade version | `bricks-install` | `deployment`, `blueprint` |
| Preview changes | `bricks-install` | `plan-only: true` |
| Auto-apply | `bricks-install` | `apply: true` |
| Find pending | `bricks-list-deployments` | `status: "PLANNED"` |
| Find failed | `bricks-list-deployments` | `status: "ERROR"` |
