# Copilot Instructions for Scrum Sample

## Repository overview

This repository follows a Scrum workflow using GitHub Projects (Projects V2).
Issues are categorized as **Epic**, **PBI** (Product Backlog Item), or **Task**.

The GitHub Project attached to this repository has custom fields including a **Sprint** field
(iteration or single-select type) that records which sprint each item belongs to.

---

## Accessing Sprint and other Project custom fields via MCP

The GitHub MCP Server's `projects` toolset lets you read and write GitHub Projects V2 data.
**Important**: `list_project_items` only returns the item title by default. To get field values
such as Sprint, you must explicitly pass the field ID in the `fields` parameter. Always follow
the two-step flow below.

### Step 1 — Find the Sprint field ID

Call `list_project_fields` for the project. Look for a field whose name is "Sprint"
(it is usually an `ITERATION` or `SINGLE_SELECT` type).

```
list_project_fields(owner: "NT-D", project_number: <N>)
```

Note the `id` value of the Sprint field.

### Step 2 — Fetch items with the Sprint value

Pass the Sprint field ID in the `fields` parameter of `list_project_items` or `get_project_item`.

```
list_project_items(owner: "NT-D", project_number: <N>, fields: ["<sprint_field_id>"])
```

Each returned item will now include the Sprint value alongside its title.

### Updating a Sprint value

Use `update_project_item_field` (part of the `projects_write` toolset) with the item ID,
field ID, and the new value (option ID for single-select, or iteration ID for iteration fields).

---

## Required MCP configuration

| Setting | Value |
|---|---|
| MCP Server | GitHub official MCP Server (`ghcr.io/github/github-mcp-server`) |
| Toolsets to enable | `repos`, `issues`, `pull_requests`, `projects` |
| Token scope (read) | `read:project` |
| Token scope (write) | `project` |

For the **Copilot cloud agent**, add the token as an environment secret named
`GITHUB_TOKEN` (or a PAT) in the repository's **`copilot` Actions environment**
(Settings → Environments → copilot → Environment secrets) with at least `read:project` scope.

For **VS Code**, see `.vscode/mcp.json` in this repository.

---

## Sprint query example (natural language prompt for Copilot Chat)

> "Look at the GitHub Project for NT-D, find the project number, then list all open PBI issues
> and their Sprint values. First get the Sprint field ID with list_project_fields, then use
> list_project_items with that field ID."
