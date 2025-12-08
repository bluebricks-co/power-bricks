# Blueprint Discovery

## Overview

Find and understand Bluebricks blueprints before deployment. Blueprints are reusable infrastructure packages built on Terraform, Helm, CloudFormation, or Bicep.

## Searching Blueprints

### Fuzzy Search

```javascript
// Search by name, tags, or description
bricks-list-blueprints({ "query": "postgres" })

// Results include: aws_rds_postgres, azure_postgres_flexible, gcp_cloud_sql_postgres
```

### With Properties

```javascript
// Include required and optional properties
bricks-list-blueprints({ "query": "kubernetes", "include_properties": true })
```

### Exact Match

```javascript
// Get specific blueprint details
bricks-list-blueprints({ "blueprint": "aws_rds_postgres", "include_properties": true })
```

## Understanding Blueprint Properties

### Property Response Structure

```json
{
  "name": "aws_rds_postgres",
  "description": "Managed PostgreSQL on AWS RDS",
  "properties": [
    {
      "name": "instance_class",
      "type": "string",
      "required": true,
      "description": "RDS instance type",
      "default": "db.t3.micro"
    },
    {
      "name": "engine_version",
      "type": "string",
      "required": false,
      "default": "15.4"
    },
    {
      "name": "password",
      "type": "string",
      "required": true,
      "sensitive": true
    }
  ]
}
```

### Property Types

| Type | Description | Example Value |
|------|-------------|---------------|
| `string` | Text value | `"db.t3.medium"` |
| `number` | Numeric value | `5432` |
| `boolean` | True/false | `true` |
| `list` | Array of values | `["subnet-a", "subnet-b"]` |
| `map` | Key-value pairs | `{"Environment": "staging"}` |

### Required vs Optional

- **Required properties** must be provided unless the environment supplies them
- **Optional properties** have defaults; only override when needed
- **Sensitive properties** should use `Secrets.*` references

## Filtering Results

### By Type

```javascript
// Blueprints only (default)
bricks-list-blueprints({ "query": "vpc", "type": "blueprint" })

// Artifacts (building blocks)
bricks-list-blueprints({ "query": "vpc", "type": "artifact" })

// Both
bricks-list-blueprints({ "query": "vpc", "type": "*" })
```

### Pagination

```javascript
// Large result sets
bricks-list-blueprints({ "query": "aws", "page": "1", "limit": "20" })

// Get all without pagination
bricks-list-blueprints({ "query": "aws", "all": true })
```

## Common Search Patterns

### By Cloud Provider

```javascript
bricks-list-blueprints({ "query": "aws" })
bricks-list-blueprints({ "query": "azure" })
bricks-list-blueprints({ "query": "gcp" })
```

### By Service Type

```javascript
bricks-list-blueprints({ "query": "database" })
bricks-list-blueprints({ "query": "kubernetes" })
bricks-list-blueprints({ "query": "networking" })
bricks-list-blueprints({ "query": "storage" })
```

### By Technology

```javascript
bricks-list-blueprints({ "query": "terraform" })
bricks-list-blueprints({ "query": "helm" })
```

## Agent Guidance

When a user asks to deploy infrastructure:

1. **Search first** - Use `bricks-list-blueprints` with relevant query terms
2. **Get properties** - Re-query with `include_properties: true` for the best match
3. **Check requirements** - Identify required properties not provided by the environment
4. **Confirm with user** - Present the blueprint and required inputs before deploying

### Example Flow

User: "I need a PostgreSQL database for staging"

```javascript
// Step 1: Find matching blueprints
bricks-list-blueprints({ "query": "postgres", "include_properties": true })

// Step 2: Check environment properties
bricks-list-environments({ "expand": true })

// Step 3: Deploy with only required overrides
bricks-install({
  "blueprint": "aws_rds_postgres",
  "environment": "staging",
  "props": "{\"instance_class\": \"db.t3.medium\", \"password\": \"Secrets.db_password\"}"
})
```

## Quick Reference

| Goal | Tool Call |
|------|-----------|
| Search blueprints | `bricks-list-blueprints({ "query": "..." })` |
| Get blueprint details | `bricks-list-blueprints({ "blueprint": "...", "include_properties": true })` |
| List all blueprints | `bricks-list-blueprints({ "all": true })` |
| Filter by type | `bricks-list-blueprints({ "type": "blueprint" })` |

