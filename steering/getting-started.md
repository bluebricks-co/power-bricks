# Get Started with Bluebricks

## Overview

Get your first environment running in minutes. Bluebricks transforms your IaC into one-click deployments.

> 💡 **Watch the demo** - See `https://vimeo.com/1142161867/258152a970` for a visual walkthrough.

## Trigger

When user says "Get started with Bluebricks", "deploy infrastructure", "set up environment", or similar.

---

## Step 1: Verify Authentication

**Before you begin:**
1. Create account at [app.bluebricks.co](https://app.bluebricks.co)
2. Install CLI: `brew install bluebricks/tap/bricks`
3. Authenticate: `bricks login`

---

## Step 2: Find Your Blueprint

Ask: "What do you want to deploy?"

```javascript
bricks-list-blueprints({
  "query": "<use case>",
  "include_properties": true,
  "limit": "10"
})
```

**Popular searches:**
- `postgres` or `database` → Database blueprints
- `vpc` or `network` → Networking
- `kubernetes` or `eks` → Container orchestration
- `s3` or `storage` → Storage solutions

**Review the results:**
- Check required properties (those without defaults)
- Note the blueprint name and version

---

## Step 3: Choose Your Environment

```javascript
bricks-list-environments({})
```

Environments organize your infrastructure—Dev, Staging, Production. Each can have:
- Different cloud accounts
- Environment-specific properties that auto-inject
- Isolated deployments

**No environments?** Create one at [app.bluebricks.co/environments](https://app.bluebricks.co/environments)

---

## Step 4: Deploy

### Get Blueprint Properties

```javascript
bricks-list-blueprints({
  "blueprint": "<selected-blueprint>",
  "include_properties": true
})
```

### Prepare Your Configuration

```json
{
  "instance_class": "db.t3.medium",
  "allocated_storage": 100
}
```

**Property tips:**
- Only set what you need—defaults handle the rest
- Use `Secrets.db_password` for sensitive data
- Environment properties auto-inject (region, naming, etc.)

### Launch

```javascript
bricks-install({
  "blueprint": "<blueprint>",
  "environment": "<environment>",
  "props": "{\"property\": \"value\"}",
  "wait-for-approval": true
})
```

**Deployment states:**
- **PLANNING** → Generating plan
- **PLANNED** → Ready for review
- **INSTALLING** → Applying changes
- **COMPLETED** → Done!

---

## Step 5: Review & Approve

```javascript
// Check the plan
bricks-deployment-status({
  "deployment": "<deployment_id>"
})
```

Review `plan_link` in browser, then:

```javascript
// If it looks good
bricks-deployment-approve({
  "deployment": "<deployment_id>",
  "task": "<task_id>"
})

// If you need changes
bricks-deployment-reject({
  "deployment": "<deployment_id>",
  "task": "<task_id>"
})
```

---

## Step 6: Monitor

```javascript
bricks-deployment-status({ "deployment": "<deployment_id>" })
```

Watch: `PLANNING → PLANNED → INSTALLING → COMPLETED`

---

## Quick Reference

| Action | Tool | Parameters |
|--------|------|------------|
| Find blueprints | `bricks-list-blueprints` | `query`, `include_properties` |
| List environments | `bricks-list-environments` | — |
| Deploy | `bricks-install` | `blueprint`, `environment`, `props` |
| Check status | `bricks-deployment-status` | `deployment` |
| Approve | `bricks-deployment-approve` | `deployment`, `task` |
| Reject | `bricks-deployment-reject` | `deployment`, `task` |

---

## What's Next?

- **Update deployments** → Use `deployment` parameter instead of `blueprint`
- **Multiple environments** → Same blueprint, different configs
- **Secrets** → Store sensitive data at environment level
- **Monitor** → `bricks-list-deployments` shows everything

---

## Common Issues

**"Blueprint not found"**
→ Use `query` for fuzzy search, check spelling

**"Environment not found"**
→ Create one at app.bluebricks.co first

**"Missing properties"**
→ Use `include_properties: true` to see requirements

**"Approval failed"**
→ Both `deployment` and `task` IDs required
