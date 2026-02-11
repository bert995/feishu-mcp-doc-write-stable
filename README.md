# feishu-mcp-doc-write-stable

Stable Feishu/Lark document writing workflow via MCP.

This skill standardizes a reliable write pipeline for cloud documents (especially PRDs) and prevents markdown corruption caused by escaped newlines (e.g. literal `\n` shown in doc body).

## What this skill solves

- ✅ Use Feishu MCP as the write path (`create-doc` / `update-doc` / `fetch-doc`)
- ✅ Enforce **real multiline markdown** input
- ✅ Add post-write validation (`fetch-doc` readback)
- ✅ Provide fallback strategy when title-based replacement is unstable

## Best for

- Writing PRDs/specs into Feishu/Lark cloud docs
- Repeated doc updates that need consistent structure
- Teams that need deterministic output over fancy formatting

## Core workflow

1. Draft content and confirm with user
2. Create doc (or use existing doc URL)
3. Write/update with real multiline markdown
4. Read back with `fetch-doc`
5. Validate headings/lists/order before finishing

## Key rule (most important)

Do **not** pass escaped markdown such as:

```text
"# Title\\n\\n## Section"
```

Use real multiline content instead (file/variable payload), then send JSON args to MCP.

## Included patterns

- Safe create-doc pattern
- Safe overwrite pattern via multiline markdown file
- Append + targeted replace pattern (`selection_with_ellipsis`)
- Validation checklist and failure handling

## Security & privacy

- This repository contains **no personal tokens**, **no private document IDs**, and **no local machine secrets**.
- All examples use placeholders and generic URLs.
- No hidden telemetry or upload logic.

## Skill file

- `SKILL.md` — operational instructions for the agent

## Related references

- Lark Docs style reference: https://skills.sh/serendipityoneinc/srp-claude-code-marketplace/lark-docs

## License

MIT
