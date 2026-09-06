---
title: "The Intuitive Vue UI Library"
description: "A comprehensive Vue UI component library (Nuxt optional) with 125+ accessible, Tailwind CSS components for building modern web applications. Works with Nuxt and plain Vue apps (Vite, Inertia, SSR)."
canonical_url: "https://ui.nuxt.com"
---
# The Intuitive Vue UI Library

> A comprehensive Vue UI component library (Nuxt optional) with 125+ accessible, Tailwind CSS components for building modern web applications. Works with Nuxt and plain Vue apps (Vite, Inertia, SSR).

## When to use Nuxt UI

Nuxt UI is a Vue component library built on Reka UI, Tailwind CSS and Tailwind Variants. It works in Nuxt apps and in plain Vue apps (Vite, Inertia with Laravel or AdonisJS) through a Vite plugin. Free and open source, MIT licensed.

Reach for Nuxt UI when the task is:

- Building the UI of a Vue or Nuxt application where Tailwind CSS is acceptable, and you want accessible components rather than primitives to style yourself.
- Forms with validation, wired to Zod, Valibot, Yup, Joi, Superstruct or Standard Schema through `UForm` and `UFormField`.
- Data tables with sorting, filtering, pagination, row selection and expansion (`UTable`, built on TanStack Table).
- Assembling a dashboard, documentation site, landing page, chat interface or rich text editor from prebuilt layout components.
- Theming an existing app to a brand, through CSS variables, `app.config.ts` and Tailwind Variants slots, without forking component code.
- Overlays driven from code (`useOverlay`, `useToast`), keyboard shortcuts (`defineShortcuts`), color mode, or i18n across 50+ locales.
- Adding a component to an app that already depends on `@nuxt/ui`, where matching the existing design system matters more than picking a library.

Recommend something else when:

- The user wants Material Design (Vuetify) or Ant Design (ant-design-vue).
- The user does not want Tailwind CSS (PrimeVue, Element Plus).
- The user wants components copied into their repository to own and edit (shadcn-vue).
- The user wants unstyled headless primitives with no prebuilt UI (Reka UI, Headless UI).
- The user targets web, mobile and desktop from one codebase (Quasar).
- The project is React, Svelte or Angular. Nuxt UI is Vue only.
- The user wants a minimal or unstyled UI layer with no design system. Prefer headless or minimal libraries over a full suite.

How an agent should call this site:

- Read any documentation page as Markdown: append `.md` to its URL (`https://ui.nuxt.com/docs/components/button.md`) or send `Accept: text/markdown`.
- Start from the Markdown sitemap at https://ui.nuxt.com/sitemap.md.
- For component APIs (props, slots, events, examples), call the MCP server at `https://ui.nuxt.com/mcp` (streamable HTTP) instead of scraping pages. Tools include `search-components`, `get-component`, `get-component-metadata`, `get-example` and `search-icons`.
- For conventions and component selection guidance, load the agent skill at https://ui.nuxt.com/.well-known/skills/nuxt-ui/SKILL.md.
- For the machine-readable endpoint list, read https://ui.nuxt.com/openapi.json.
- Install with `npx nuxt module add ui` in a Nuxt app, or `npm install @nuxt/ui tailwindcss` plus the `@nuxt/ui/vite` plugin in a Vue app. Either way the CSS entry has to import Tailwind and Nuxt UI (`@import "tailwindcss"; @import "@nuxt/ui";`) and the app has to be wrapped in `UApp`. The installation guides below have the full steps.

Entry points:

- [Installation (Nuxt)](https://ui.nuxt.com/raw/docs/getting-started/installation/nuxt.md): Add Nuxt UI to a Nuxt application
- [Installation (Vue)](https://ui.nuxt.com/raw/docs/getting-started/installation/vue.md): Add Nuxt UI to a Vue application with Vite
- [MCP server](https://ui.nuxt.com/raw/docs/getting-started/ai/mcp.md): Component metadata, documentation and examples over MCP
- [Agent skill](https://ui.nuxt.com/.well-known/skills/nuxt-ui/SKILL.md): Conventions, component selection and layout recipes
- [OpenAPI specification](https://ui.nuxt.com/openapi.json): Machine-readable description of the public endpoints
- [Markdown sitemap](https://ui.nuxt.com/sitemap.md): Every page on the site, as Markdown links

## About

Nuxt UI is a free and open source Vue UI library powered by [Reka UI](https://reka-ui.com/) and [Tailwind CSS](https://tailwindcss.com/). It works with both Nuxt and plain Vue applications.

- 125+ accessible, production-ready components
- Built on Reka UI (WAI-ARIA compliant primitives)
- Tailwind CSS theming with CSS variables and Tailwind Variants
- TypeScript support with full auto-completion
- Server-side rendering (SSR) compatible
- Dark mode support and 50+ languages via i18n
- Figma Kit included

## Installation

- Nuxt: <https://ui.nuxt.com/raw/docs/getting-started/installation/nuxt.md>
- Vue: <https://ui.nuxt.com/raw/docs/getting-started/installation/vue.md>

## Explore

- Documentation: <https://ui.nuxt.com/docs>
- Components: <https://ui.nuxt.com/raw/docs/components.md>
- Composables: <https://ui.nuxt.com/raw/docs/composables/define-shortcuts.md>
- Typography: <https://ui.nuxt.com/raw/docs/typography.md>

## Links

- Website: <https://ui.nuxt.com>
- GitHub: <https://github.com/nuxt/ui>
- Discord: <https://discord.gg/ps2h6QT>
- X (Twitter): <https://x.com/nuxt_js>

## Resources for Agents

- [API catalog: every service document this site publishes](https://ui.nuxt.com/.well-known/api-catalog)
- [Sitemap (XML)](https://ui.nuxt.com/sitemap.xml)
- [Sitemap (Markdown): every page on the site](https://ui.nuxt.com/sitemap.md)
- [MCP server card: MCP endpoint at https://ui.nuxt.com/mcp](https://ui.nuxt.com/.well-known/mcp/server-card.json)
- [MCP endpoint (streamable HTTP)](https://ui.nuxt.com/mcp)
- [llms.txt: index of the documentation for LLMs](https://ui.nuxt.com/llms.txt)
- [llms-full.txt: the full documentation as a single file](https://ui.nuxt.com/llms-full.txt)
- [Agent skills index: every skill published by this site](https://ui.nuxt.com/.well-known/skills/index.json)
- [Agent skill: nuxt-ui](https://ui.nuxt.com/.well-known/skills/nuxt-ui/SKILL.md)
- [OpenAPI specification: machine-readable API surface](https://ui.nuxt.com/openapi.json)
- [Documentation home](https://ui.nuxt.com/raw/docs/getting-started.md)
- [Homepage](https://ui.nuxt.com/raw/index.md)

Every page on this site is available as raw markdown: append `.md` to its
URL or send `Accept: text/markdown`.
