# Langfuse Docs

From `https://langfuse.com/llms.txt`.

## Path Mapping

Strip the domain; map the URL path as-is.

A nested path:

`https://langfuse.com/docs/administration/audit-logs.md` → `docs/administration/audit-logs.md`

Top-level entry pages map to the `Langfuse/` root — **siblings** of their same-named dirs, not nested:

- `https://langfuse.com/docs.md` → `docs.md`
- `https://langfuse.com/integrations.md` → `integrations.md`
- `https://langfuse.com/self-hosting.md` → `self-hosting.md`

## Index Source

| Source | URLs | Maps to |
| --- | --- | --- |
| `llms-docs.txt` | 99 | `docs.md` + `docs/` |
| `llms-integrations.txt` | 109 | `integrations.md` + `integrations/` |
| `llms-self-hosting.txt` | 44 | `self-hosting.md` + `self-hosting/` |

