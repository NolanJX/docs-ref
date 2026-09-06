---
title: "extractShortcuts"
description: "A utility to extract keyboard shortcuts from menu items."
canonical_url: "https://ui.nuxt.com/docs/composables/extract-shortcuts"
---
# extractShortcuts

> A utility to extract keyboard shortcuts from menu items.

## Usage

Use the auto-imported `extractShortcuts` utility to define keyboard shortcuts from menu items. It extracts shortcuts from components like [DropdownMenu](https://ui.nuxt.com/docs/components/dropdown-menu), [ContextMenu](https://ui.nuxt.com/docs/components/context-menu) or [CommandPalette](https://ui.nuxt.com/docs/components/command-palette) where items have `kbds` defined.

```vue
<script setup lang="ts">
const items = [{
  label: 'Save',
  icon: 'i-lucide-file-down',
  kbds: ['meta', 'S'],
  onSelect() {
    save()
  }
}, {
  label: 'Copy',
  icon: 'i-lucide-copy',
  kbds: ['meta', 'C'],
  onSelect() {
    copy()
  }
}]

defineShortcuts(extractShortcuts(items))
</script>
```

> [!TIP]
> See: /docs/composables/define-shortcuts
> 
> Learn more about keyboard shortcuts in the **defineShortcuts** composable documentation.

## API

`extractShortcuts(items: any[] | any[][], separator?: '_' | '-'): ShortcutsConfig`

Extracts keyboard shortcuts from an array of menu items and returns a configuration object compatible with `defineShortcuts`.

#### Parameters

**items** (`any[] | any[][]`) *required*: An array of menu items (or nested arrays) containing shortcut definitions. Each item can have the following properties:An array of keyboard keys that form the shortcut (e.g. ['meta', 'S']).A callback function to execute when the shortcut is triggered.An alternative callback function (used if onSelect is not defined).Nested menu items to recursively extract shortcuts from.Alternative property for nested menu items.

**separator** (`'_' | '-'`): The separator used to join keyboard keys. Use '_' for key combinations (e.g. meta_k) or '-' for key sequences (e.g. g-d). Defaults to '_'.

**Returns:** A `ShortcutsConfig` object that can be passed directly to `defineShortcuts`.

## Examples

### With nested items

The utility recursively traverses `children` and `items` properties to extract shortcuts from nested menu structures.

```vue
<script setup lang="ts">
import type { DropdownMenuItem } from '@nuxt/ui'

const items: DropdownMenuItem[][] = [[{
  label: 'Edit',
  icon: 'i-lucide-pencil',
  kbds: ['E'],
  onSelect() {
    edit()
  }
}, {
  label: 'Duplicate',
  icon: 'i-lucide-copy',
  kbds: ['D'],
  onSelect() {
    duplicate()
  }
}], [{
  label: 'Invite users',
  icon: 'i-lucide-user-plus',
  children: [[{
    label: 'Invite by email',
    icon: 'i-lucide-send-horizontal',
    kbds: ['meta', 'E'],
    onSelect() {
      inviteByEmail()
    }
  }, {
    label: 'Invite by link',
    icon: 'i-lucide-link',
    kbds: ['meta', 'I'],
    onSelect() {
      inviteByLink()
    }
  }]]
}], [{
  label: 'Delete',
  icon: 'i-lucide-trash',
  kbds: ['meta', 'backspace'],
  onSelect() {
    remove()
  }
}]]

defineShortcuts(extractShortcuts(items))
</script>

<template>
  <UDropdownMenu :items="items">
    <UButton label="Actions" />
  </UDropdownMenu>
</template>
```

### With key sequences

Use the `separator` parameter to create key sequences instead of key combinations.

```vue
<script setup lang="ts">
const items = [{
  label: 'Go to Dashboard',
  kbds: ['G', 'D'],
  onSelect() {
    navigateTo('/dashboard')
  }
}, {
  label: 'Go to Settings',
  kbds: ['G', 'S'],
  onSelect() {
    navigateTo('/settings')
  }
}]

// Using '-' creates key sequences: 'g-d', 'g-s'
defineShortcuts(extractShortcuts(items, '-'))
</script>
```


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
