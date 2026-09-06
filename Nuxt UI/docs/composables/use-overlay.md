---
title: "useOverlay"
description: "A composable to programmatically control overlays."
canonical_url: "https://ui.nuxt.com/docs/composables/use-overlay"
---
# useOverlay

> A composable to programmatically control overlays.

## Usage

Use the auto-imported `useOverlay` composable to programmatically control [Modal](https://ui.nuxt.com/docs/components/modal) and [Slideover](https://ui.nuxt.com/docs/components/slideover) components.

```vue [UseOverlayExample.vue]
<script setup lang="ts">
import { LazyUseOverlayModalExample } from '#components'

const overlay = useOverlay()

const modal = overlay.create(LazyUseOverlayModalExample)
</script>

<template>
  <UButton label="Open modal" @click="modal.open()" />
</template>
```

- The `useOverlay` composable is created using `createSharedComposable`, ensuring that the same overlay state is shared across your entire application.

> [!NOTE]
> 
> Await `overlay.open()` to get a value back from the overlay. This only works if the **overlay component emits a close event**. See the example below for details.

## API

`useOverlay()`

The `useOverlay` composable provides methods to manage overlays globally. Each created overlay returns an instance with its own methods.

### create()

`create(component: T, options?: OverlayOptions<ComponentProps<T>>): OverlayInstance<T>`

Create an overlay, and return a factory instance.

#### Parameters

**component** (`T`) *required*: The overlay component to render.

**options** (`OverlayOptions`): Configuration options for the overlay.Open the overlay immediately after being created. Defaults to false.An optional object of props to pass to the rendered component.Removes the overlay from memory when closed. Defaults to false.

### open()

`open(id: symbol, props?: ComponentProps<T>): OpenedOverlay<T>`

Open an overlay by its `id`.

#### Parameters

**id** (`symbol`) *required*: The identifier of the overlay.

**props** (`ComponentProps<T>`): An optional object of props to pass to the rendered component.

### close()

`close(id: symbol, value?: any): void`

Close an overlay by its `id`.

#### Parameters

**id** (`symbol`) *required*: The identifier of the overlay.

**value** (`any`): A value to resolve the overlay promise with.

### closeAll()

`closeAll(): void`

Close all open overlays.

### patch()

`patch(id: symbol, props: Partial<ComponentProps<T>>): void`

Update an overlay by its `id`.

#### Parameters

**id** (`symbol`) *required*: The identifier of the overlay.

**props** (`Partial<ComponentProps<T>>`) *required*: An object of props to update on the rendered component.

### unmount()

`unmount(id: symbol): void`

Remove an overlay from the DOM by its `id`.

#### Parameters

**id** (`symbol`) *required*: The identifier of the overlay.

### isOpen()

`isOpen(id: symbol): boolean`

Check if an overlay is open using its `id`.

#### Parameters

**id** (`symbol`) *required*: The identifier of the overlay.

### overlays

`overlays: Overlay[]`

In-memory list of all overlays that were created.

## Instance API

These are the methods available on the instance returned by `create()`.

### open()

`open(props?: ComponentProps<T>): OpenedOverlay<T>`

Open the overlay. Returns an `OpenedOverlay`, a Promise that resolves with the value emitted by the `close` event. The same promise is also exposed as `result`, so `const { result } = modal.open()` works too.

#### Parameters

**props** (`ComponentProps<T>`): An optional object of props to pass to the rendered component.

```vue
<script setup lang="ts">
import { LazyModalExample } from '#components'

const overlay = useOverlay()

const modal = overlay.create(LazyModalExample)

function openModal() {
  modal.open({
    title: 'Welcome'
  })
}
</script>
```

### close()

`close(value?: any): void`

Close the overlay.

#### Parameters

**value** (`any`): A value to resolve the overlay promise with.

### patch()

`patch(props: Partial<ComponentProps<T>>): void`

Update the props of the overlay.

#### Parameters

**props** (`Partial<ComponentProps<T>>`) *required*: An object of props to update on the rendered component.

```vue
<script setup lang="ts">
import { LazyModalExample } from '#components'

const overlay = useOverlay()

const modal = overlay.create(LazyModalExample, {
  props: { title: 'Welcome' }
})

function openModal() {
  modal.open()
}

function updateModalTitle() {
  modal.patch({ title: 'Updated Title' })
}
</script>
```

## Examples

### With multiple overlays

This example demonstrates how to manage multiple overlays and pass data between them:

```vue
<script setup lang="ts">
import { ModalA, ModalB, SlideoverA } from '#components'

const overlay = useOverlay()

// Create with default props
const modalA = overlay.create(ModalA, { props: { title: 'Welcome' } })
const modalB = overlay.create(ModalB)
const slideoverA = overlay.create(SlideoverA)

const openModalA = () => {
  // Open modalA, but override the title prop
  modalA.open({ title: 'Hello' })
}

const openModalB = async () => {
  // Open modalB, and wait for its result
  const input = await modalB.open()

  // Pass the result from modalB to the slideover, and open it
  slideoverA.open({ input })
}
</script>

<template>
  <UButton label="Open Modal" @click="openModalA" />
</template>
```

### Confirm dialog

This example demonstrates how to create a reusable confirm dialog pattern using a custom `useConfirmDialog` composable that wraps `useOverlay`. This approach enables opinionated dialogs tailored to specific business requirements and design preferences.

1. Create a `ConfirmDialog` component that emits a boolean value when closed:

```vue [components/ConfirmDialog.vue]
<script lang="ts" setup>
interface ConfirmDialogProps {
  title?: string
  description?: string
}

defineProps<ConfirmDialogProps>()

const emits = defineEmits<{
  close: [value: boolean]
}>()
</script>

<template>
  <UModal
    :title="title"
    :description="description"
    :dismissible="false"
    :ui="{ footer: 'justify-end' }"
  >
    <template #footer>
      <UButton label="Cancel" color="neutral" variant="outline" @click="emits('close', false)" />
      <UButton label="Confirm" color="neutral" @click="emits('close', true)" />
    </template>
  </UModal>
</template>
```

1. Create a `useConfirmDialog` composable that returns a Promise:

```ts [composables/useConfirmDialog.ts]
import { ConfirmDialog } from '#components'

export interface ConfirmDialogOptions {
  title: string
  description?: string
}

export const useConfirmDialog = () => {
  const overlay = useOverlay()

  return (options: ConfirmDialogOptions): Promise<boolean> => {
    const modal = overlay.create(ConfirmDialog, {
      destroyOnClose: true,
      props: options
    })

    return modal.open()
  }
}
```

1. Use the composable in your components:

```vue
<script setup lang="ts">
const confirm = useConfirmDialog()

const handleDelete = async () => {
  const confirmed = await confirm({
    title: 'Delete item',
    description: 'Are you sure you want to delete this item?'
  })

  if (confirmed) {
    console.log('Item deleted')
  }
}
</script>

<template>
  <UButton label="Delete item" @click="handleDelete" />
</template>
```

## Caveats

### Provide / Inject

When opening overlays programmatically (modals, slideovers and so on), the overlay component can only access injected values from the component containing `UApp` (typically `app.vue` or layout components). This is because overlays are mounted outside of the page context by the `UApp` component.

As such, using `provide()` in pages or parent components isn't supported directly. To pass provided values to overlays, the recommended approach is to use props instead:

```vue
<script setup lang="ts">
import { LazyModalExample } from '#components'

const overlay = useOverlay()

const providedValue = inject('valueProvidedInPage')

const modal = overlay.create(LazyModalExample, {
  props: {
    providedValue
  }
})
</script>
```


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
