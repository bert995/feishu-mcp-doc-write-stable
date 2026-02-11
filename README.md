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

## Feishu MCP 开通流程（给最终用户）

参考官方文档：
- https://open.larkoffice.com/document/mcp_open_tools/end-user-call-remote-mcp-server

推荐最小流程：

1. 在飞书开放平台创建/选择应用（企业自建应用）。
2. 在应用里开启 MCP 相关能力（Remote MCP，Beta 能力以控制台实际入口为准）。
3. 配置并发布可调用的 MCP Server（拿到 `mcpServers` URL）。
4. 用终端工具（如 `mcporter`）完成用户授权并验证工具列表。
5. 先做最小可用验证：`get-user` -> `create-doc` -> `update-doc` -> `fetch-doc`。
6. 验证通过后再接入完整 PRD 工作流。

> 注意：不同租户、版本和灰度策略下，控制台入口名称会有差异，请以官方控制台与文档为准。

## 飞书应用权限建议（最小可用集）

按“最小权限原则”只开你实际用到的范围。若你需要本技能完整流程（创建/写入/读取文档），通常至少需要：

- 云文档读权限（读取内容、校验回读）
- 云文档写权限（创建与更新正文）
- 云文档块转换/渲染相关权限（如 markdown 转块）
- 云空间/云盘相关权限（文档创建与目录挂载场景）

若需要扩展能力，再按需增加：

- Wiki 读取/写入（知识库场景）
- 评论读取/写入（评审流）
- 用户信息查询（`search-user` / `get-user` / @提及）

> 英文 scope 名称会随 API/版本变化，请在开放平台权限页按 API 提示逐项勾选，并通过一次真实调用回归验证。

## 中文快速上手（3 分钟）

1. 在飞书开放平台创建/选择一个企业自建应用。
2. 在应用中开通 MCP 远程调用能力（以控制台实际入口为准）。
3. 按最小权限原则开启文档读写相关权限并发布版本。
4. 获取 MCP Server URL（`mcpServers` 配置）。
5. 本地用 `mcporter` 验证：先 `get-user`，再 `create-doc`、`update-doc`、`fetch-doc`。
6. 写入时始终用“真实多行 markdown”，不要传 `\n` 字符串。

通过以上 6 步后，即可稳定执行 PRD 写入与回读校验流程。

## License

MIT
