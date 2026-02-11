---
name: feishu-mcp-doc-write-stable
description: Stable Feishu doc writing via MCP (mcporter + lark.update-doc/create-doc) with real multiline markdown and post-write fetch validation. Use when user asks to write/update PRD or cloud docs through Feishu MCP while avoiding literal \n formatting corruption.
---

# Feishu MCP Stable Writer

Use this workflow to keep Feishu MCP doc output readable and deterministic.

## Rules

1. Always use Feishu MCP (`mcporter call lark.*`) for create/update/read in this flow.
2. Never pass markdown as escaped `\\n` text.
3. Always send **real multiline markdown**.
4. Always run `fetch-doc` after write and verify structure.

## Standard Flow

1. Draft content in chat and get user confirmation.
2. Create doc (or use given doc URL).
3. Write/update with real multiline markdown.
4. Read back and verify headings/lists/order.
5. Report concise result + doc URL.

## Command Patterns

### A) Create doc

```bash
mcporter call lark.create-doc \
  title='文档标题' \
  wiki_space='my_library' \
  markdown='# 占位\n\n准备写入' \
  --output json
```

### B) Safe overwrite with real multiline markdown

```bash
cat > /tmp/doc.md <<'MD'
# 标题

## 1. 背景
正文

## 2. 目标
- A
- B
MD

python3 - <<'PY'
import json, shlex, subprocess
from pathlib import Path
md = Path('/tmp/doc.md').read_text()
args = json.dumps({
  'doc_id': 'https://www.feishu.cn/wiki/xxxxx',
  'mode': 'overwrite',
  'markdown': md
}, ensure_ascii=False)
cmd = f"mcporter call lark.update-doc --args {shlex.quote(args)} --output json"
print(subprocess.check_output(cmd, shell=True, text=True))
PY
```

### C) Append + precise replace

```bash
mcporter call lark.update-doc doc_id='https://www.feishu.cn/wiki/xxxxx' \
  mode='append' \
  markdown='## 新章节
内容' \
  --output json

mcporter call lark.update-doc doc_id='https://www.feishu.cn/wiki/xxxxx' \
  mode='replace_range' \
  selection_with_ellipsis='## 2. 目标...- B' \
  markdown='## 2. 目标
- A（修订）
- B（修订）
- C（新增）' \
  --output json
```

## Validation Checklist

- `fetch-doc` output does not contain literal `\\n` in body text.
- Heading hierarchy is preserved.
- Lists render as lists (not plain joined text).
- No missing section after update.

## Failure Handling

- If `selection_by_title` fails, use `fetch-doc` then switch to `selection_with_ellipsis`.
- If format drifts, rebuild markdown in `/tmp/*.md` and overwrite once.
- If permissions fail, confirm MCP URL/auth and retry with same user identity.
