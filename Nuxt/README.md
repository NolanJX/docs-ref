# Nuxt Docs

From `https://nuxt.com/llms.txt`.

Read `llms.txt` and its sub-files; `llms-full.txt` (all pages concatenated) is an archive, not for reading.

## Path Mapping

Strip the domain and the `raw/` prefix; map the remaining URL path as-is.

A nested path:

`https://nuxt.com/raw/docs/4.x/getting-started/introduction.md` → `docs/4.x/getting-started/introduction.md`

Top-level pages:

- `https://nuxt.com/llms.txt` → `llms.txt`
- `https://nuxt.com/llms-full.txt` → `llms-full.txt`

## Index Source

| Source | URLs | Maps to |
| --- | --- | --- |
| `llms.txt` | 352 | `docs/` + `blog/` + `deploy/` |

