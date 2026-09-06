# Nuxt UI Docs

From `https://ui.nuxt.com/llms.txt`.

Read `llms.txt` and its sub-files; `llms-full.txt` (all pages concatenated) is an archive, not for reading.

## Path Mapping

Strip the domain and the `raw/` prefix; map the remaining URL path as-is.

A nested path:

`https://ui.nuxt.com/raw/docs/getting-started/installation/nuxt.md` → `docs/getting-started/installation/nuxt.md`

A nested path without the `raw/` prefix:

`https://ui.nuxt.com/.well-known/skills/nuxt-ui/SKILL.md` → `.well-known/skills/nuxt-ui/SKILL.md`

Top-level pages:

- `https://ui.nuxt.com/llms.txt` → `llms.txt`
- `https://ui.nuxt.com/llms-full.txt` → `llms-full.txt`
- `https://ui.nuxt.com/sitemap.md` → `sitemap.md`

## Index Source

| Source | URLs | Maps to |
| --- | --- | --- |
| `llms.txt` | 186 | `index.md` + `docs/` + `sitemap.md` + `.well-known/` |
