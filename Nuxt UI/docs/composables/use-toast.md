---
title: "useToast"
description: "A composable to display toast notifications in your app."
canonical_url: "https://ui.nuxt.com/docs/composables/use-toast"
---
# useToast

> A composable to display toast notifications in your app.

## Usage

Use the auto-imported `useToast` composable to display [Toast](https://ui.nuxt.com/docs/components/toast) notifications.

```vue [UseToastExample.vue]
<script setup lang="ts">
const toast = useToast()

function showToast() {
  toast.add({
    title: 'New message',
    description: 'You have a new message from Benjamin.',
    actions: [{
      icon: 'i-lucide-reply',
      label: 'Reply',
      color: 'neutral',
      variant: 'outline',
      onClick: () => {
        toast.add({ title: 'Reply sent', color: 'success' })
      }
    }]
  })
}
</script>

<template>
  <UButton label="Show toast" @click="showToast" />
</template>
```

- The `useToast` composable uses Nuxt's `useState` to manage the toast state, ensuring reactivity across your application.
- A maximum of 5 toasts are displayed at a time by default. When adding a new toast that would exceed this limit, the oldest toast is automatically removed. Change it with the `toaster.max` prop on the [`App`](https://ui.nuxt.com/docs/components/app#props) component.
- When removing a toast, there's a 200ms delay before it's actually removed from the state, allowing for exit animations.

> [!WARNING]
> 
> Make sure to wrap your app with the [`App`](https://ui.nuxt.com/docs/components/app) component which uses our [`Toaster`](https://github.com/nuxt/ui/blob/v4/src/runtime/components/Toaster.vue) component which uses the [`ToastProvider`](https://reka-ui.com/docs/components/toast#provider) component from Reka UI.

> [!TIP]
> See: /docs/components/toast
> 
> Learn how to customize the appearance and behavior of toasts in the **Toast** component documentation.

## API

`useToast()`

The `useToast` composable provides methods to manage toast notifications globally.

### add()

`add(toast: Partial<Toast>): Toast`

Adds a new toast notification.

#### Parameters

**toast** (`Partial<Toast>`) *required*: A partial Toast object with the following properties:A unique identifier for the toast. If not provided, a unique id is generated. Reusing an existing id merges into that toast instead of adding a new one.Whether the toast is open. Defaults to true.The title displayed in the toast.The description displayed in the toast.The icon displayed in the toast.The avatar displayed in the toast. See Avatar.The color of the toast. Defaults to primary.The orientation between the content and the actions. Defaults to vertical.Customize or hide the close button (with false value). Defaults to true.The icon displayed in the close button.The actions displayed in the toast. See Button.Customize or hide the progress bar (with false value). Defaults to true.The duration in milliseconds before the toast auto-closes. Defaults to 5000. Set to 0 to keep the toast open until it's manually closed. Can also be set globally on the App component.A callback function invoked when the toast is clicked.A callback function invoked when the toast open state changes. Useful to perform an action when the toast closes (expired or dismissed).How assistive technologies announce the toast. Use background for toasts that aren't the result of a direct user action.The element or component the toast renders as. Defaults to li.

**Returns:** The complete `Toast` object that was added.

```vue
<script setup lang="ts">
const toast = useToast()

function showToast() {
  toast.add({
    title: 'Success',
    description: 'Your action was completed successfully.',
    color: 'success'
  })
}
</script>
```

### update()

`update(id: string | number, toast: Omit<Partial<Toast>, 'id'>): void`

Updates an existing toast notification.

#### Parameters

**id** (`string | number`) *required*: The unique identifier of the toast to update.

**toast** (`Omit<Partial<Toast>, 'id'>`) *required*: A partial Toast object with the properties to update. The id cannot be changed, the toast is reopened, and duration is reset unless you pass it again.

```vue
<script setup lang="ts">
const toast = useToast()

function updateToast(id: string | number) {
  toast.update(id, {
    title: 'Updated Toast',
    description: 'This toast has been updated.'
  })
}
</script>
```

### remove()

`remove(id: string | number): void`

Removes a toast notification.

#### Parameters

**id** (`string | number`) *required*: The unique identifier of the toast to remove.

```vue
<script setup lang="ts">
const toast = useToast()

function removeToast(id: string | number) {
  toast.remove(id)
}
</script>
```

### clear()

`clear(): void`

Removes all toast notifications.

```vue
<script setup lang="ts">
const toast = useToast()

function clearAllToasts() {
  toast.clear()
}
</script>
```

### toasts

`toasts: Ref<Toast[]>`

A reactive array containing all current toast notifications.

```vue
<script setup lang="ts">
const { toasts } = useToast()
</script>

<template>
  <div>
    <pre>{{ toasts }}</pre>
  </div>
</template>
```


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
