---
title: "ProsePrompt"
description: "Display pre-built AI prompts with one-click copy and IDE integration."
canonical_url: "https://ui.nuxt.com/docs/typography/prompt"
---
# ProsePrompt

> Display pre-built AI prompts with one-click copy and IDE integration.

## Usage

Use the `prompt` component to display a pre-built AI prompt that users can copy to their clipboard or open directly in their IDE. The `description` prop is shown as the visible label, while the default slot contains the prompt text that gets copied.

```mdc
::prompt{description="Build a dashboard layout with Nuxt UI."}
You are a Nuxt UI expert. Help me build a dashboard layout with a collapsible sidebar and a sticky top navbar.

Requirements:
- Use `UDashboardPanel`, `UDashboardSidebar`, and `UDashboardNavbar`
- Use semantic color tokens like `bg-elevated` and `text-muted` for theming
- The sidebar should include navigation links with icons using `UNavigationMenu`
- The navbar should display a breadcrumb, a search button, and a user dropdown menu
- The layout must be fully responsive and collapse the sidebar on mobile
::
```

### Icon

Use the `icon` prop to display an icon next to the description.

```mdc
::prompt{description="Create a form with validation." icon="i-lucide-file-pen-line"}
Create a registration form using Nuxt UI with Zod schema validation.

Requirements:
- Use `UForm` with a Zod schema for validation
- Add `UFormField` wrapping each input: name (`UInput`), email (`UInput` type email), role (`USelect` with options Admin, Editor, Viewer)
- Include a submit `UButton` with loading state
- Display inline error messages below each field
- On successful submit, show a `UToast` notification
::
```

### Actions

Use the `actions` prop to display additional buttons. The `copy` button is always displayed. The available actions are `cursor`, `windsurf` and `claude`.

```mdc
::prompt{description="Add a color mode toggle." icon="i-lucide-sun-moon" actions="cursor,claude"}
Add a color mode toggle to my Nuxt app.

Requirements:
- Use `useColorMode` from `@nuxtjs/color-mode` to manage the current mode
- Render a `UButton` with `variant="ghost"` that cycles between `light`, `dark`, and `system` on click
- Update the button icon dynamically: `i-lucide-sun` for light, `i-lucide-moon` for dark, `i-lucide-monitor` for system
- Add a tooltip using `UTooltip` that shows the current active mode
::
```

## API

### Props

```ts
/**
 * Props for the ProseProsePrompt component
 */
interface ProseProsePromptProps {
  description?: string | undefined;
  icon?: any;
  /**
   * The `copy` action is always displayed, list any additional actions to show alongside it.
   * @default []
   */
  actions?: ("copy" | "cursor" | "windsurf" | "claude")[] | undefined;
  ui?: { root?: SlotClass; icon?: SlotClass; content?: SlotClass; description?: SlotClass; actions?: SlotClass; } | undefined;
}
```

### Slots

```ts
/**
 * Slots for the ProsePrompt component
 */
interface ProsePromptSlots {
  default(): any;
}
```

## Theme

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    prose: {}
  }
})
```

## Changelog

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/prose/ProsePrompt.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/prose/prose-prompt.ts).

---

- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/prose/Prompt.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
