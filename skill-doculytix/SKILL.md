---
name: skill-doculytix
description: "Use the mcp-doculytix MCP server (HachiAI) to extract structured data or answer questions about PDFs/images on disk via document_qna, ask_document, or check_trace_status. Trigger for local-file document Q&A, extraction prompts, or trace polling—not for plain in-context text, simple file reads, or multi-doc comparison without tools."
---

# mcp-doculytix: AI-Powered Document Intelligence

Extract and analyze documents using the HachiAI LLM API through Model Context Protocol (MCP).

**MCP Package:** `@hachiai/mcp-doculytix`

HachiAI document Q&A over **local files** via MCP. **`file_path`** must be readable on the **machine where the MCP server runs** (not Claude upload paths like `/mnt/user-data/uploads/`).

## When to use

- Extract fields, tables, or structured data from PDFs/images on disk.
- Natural-language Q&A that needs full-document understanding.
- Long jobs: submit with `document_qna`, then poll with `check_trace_status`.

**Do not use** when the text is already in context, the user only needs a simple read/summary without the MCP tools, or the file is not available on the MCP host.

## Pick a tool

| Goal | Tool |
|------|------|
| One call: question → wait → normalized `answer` | `ask_document` |
| Submit only; get `trace_id` immediately (heavy/large docs) | `document_qna` |
| Latest status/payload for a known `trace_id` | `check_trace_status` |

- Prefer **`ask_document`** for shorter/lighter documents.
- Prefer **`document_qna`** then **`check_trace_status`** when a single long poll is risky; poll until terminal state, then read payload.
- If one tool fails (timeout, empty result), try the other with the same intent (`question` ↔ `prompt`).

## Tools

### `document_qna`

- **`file_path`** (required): Absolute path on the **MCP server host** (e.g. `D:\docs\a.pdf`, `/home/user/a.pdf`).
- **`prompt`** (required): Extraction or analysis instructions; say desired output shape (e.g. JSON keys).

Returns the **raw submit response** (typically includes `trace_id`); does **not** poll.

### `ask_document`

- **`file_path`** (required): Same rules as above.
- **`question`** (required): Natural-language question; semantically like `prompt` in `document_qna`.

Returns JSON such as `{ "success": true, "answer": "..." }` or `{ "success": false, "error": "..." }` after internal polling.

### `check_trace_status`

- **`trace_id`** (required): From `document_qna` or prior submit.

Returns **raw** trace JSON (`status` may be `pending`, `fulfilled`, `failed`; answer fields vary by backend).

## Prompting

- State output format explicitly (e.g. JSON keys, bullet list).
- Narrow scope when useful (sections, page ranges).
- For structured extraction, a short example of the target shape helps.

## Env (reference)

Server expects **`DOC_MCP_API_KEY`**. Optional: `DOC_MCP_BASE_URL` (default `https://llm.hachiai.com`), `DOC_MCP_POLL_INTERVAL_MS`, `DOC_MCP_MAX_POLL_ATTEMPTS` (used by `ask_document` polling).

| Variable | Required | Default | Purpose |
|----------|----------|---------|---------|
| `DOC_MCP_API_KEY` | Yes | — | HachiAI API authentication key |
| `DOC_MCP_BASE_URL` | No | `https://llm.hachiai.com` | API base URL |
| `DOC_MCP_POLL_INTERVAL_MS` | No | `3000` | Milliseconds between poll attempts |
| `DOC_MCP_MAX_POLL_ATTEMPTS` | No | `120` | Max polls before timeout (~6 min at default interval) |

## Troubleshooting

| Issue | Likely cause | What to do |
|-------|----------------|------------|
| `Path not found` or `Permission denied` | Wrong path or MCP host cannot read the file | Use an absolute path on the machine running the MCP server; check permissions |
| `401 Unauthorized` | Missing or invalid API key | Set `DOC_MCP_API_KEY` correctly in the MCP server environment |
| Timeout after ~6 minutes | Large/slow job or defaults too tight | Raise `DOC_MCP_MAX_POLL_ATTEMPTS` and/or `DOC_MCP_POLL_INTERVAL_MS`, or use `document_qna` + `check_trace_status` |
| Empty or incomplete answer | Vague prompt or unsupported/problem file | Refine the prompt and output format; confirm PDF/image/text is valid |

## File paths and common mistakes

**Use a path the MCP server can read:**

- **Windows (local):** e.g. `D:\documents\report.pdf`
- **macOS/Linux (local):** e.g. `/Users/alice/documents/report.pdf`
- **Server/network:** path as seen from the host where MCP runs

**Critical:** `/mnt/user-data/uploads/` is for Claude.ai’s environment. If MCP runs on your PC or another host, use **that** machine’s real path—not the chat upload path.

| Wrong | Right |
|-------|--------|
| Using a Claude-only upload path while MCP runs locally | Same file’s absolute path on the MCP host |
| Relative or ambiguous paths when the server cwd differs | Absolute `file_path` |

## Support and resources

- **HachiAI docs:** https://docs.hachiai.com
- **MCP specification:** https://modelcontextprotocol.io
- **npm package:** https://www.npmjs.com/package/@hachiai/mcp-doculytix
- **Node.js:** ≥ 18.0.0
