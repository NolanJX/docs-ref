---
title: "RadioGroup"
description: "A set of radio buttons to select a single option from a list."
canonical_url: "https://ui.nuxt.com/docs/components/radio-group"
---
# RadioGroup

> A set of radio buttons to select a single option from a list.

## Usage

Use the `v-model` directive to control the value of the RadioGroup or the `default-value` prop to set the initial value when you do not need to control its state.

```vue
<script setup lang="ts">
const items = ref([
  "System",
  "Light",
  "Dark"
])
const value = ref("System")
</script>

<template>
  <URadioGroup v-model="value" :items="items" />
</template>
```

### Items

Use the `items` prop as an array of strings or numbers:

```vue
<script setup lang="ts">
const items = ref([
  "System",
  "Light",
  "Dark"
])
const value = ref("System")
</script>

<template>
  <URadioGroup v-model="value" :items="items" />
</template>
```

You can also pass an array of objects with the following properties:

- `label?: string`
- `description?: string`
- [`value?: string`](#value-key)
- `disabled?: boolean`
- [`icon?: string`](#indicator)
- `class?: any`
- `ui?: { item?: ClassNameValue, container?: ClassNameValue, base?: ClassNameValue, 'indicator'?: ClassNameValue, wrapper?: ClassNameValue, label?: ClassNameValue, icon?: ClassNameValue, description?: ClassNameValue }`

```vue
<script setup lang="ts">
import type { RadioGroupItem } from '@nuxt/ui'

const items = ref<RadioGroupItem[]>([
  {
    label: "System",
    description: "Matches your device settings.",
    value: "system"
  },
  {
    label: "Light",
    description: "Always uses the light theme.",
    value: "light"
  },
  {
    label: "Dark",
    description: "Always uses the dark theme.",
    value: "dark"
  }
])
const value = ref("system")
</script>

<template>
  <URadioGroup v-model="value" :items="items" />
</template>
```

> [!CAUTION]
> 
> When using objects, you need to reference the `value` property of the object in the `v-model` directive or the `default-value` prop.

### Value Key

You can change the property that is used to set the value by using the `value-key` prop. Defaults to `value`.

```vue
<script setup lang="ts">
import type { RadioGroupItem } from '@nuxt/ui'

const items = ref<RadioGroupItem[]>([
  {
    label: "System",
    description: "Matches your device settings.",
    id: "system"
  },
  {
    label: "Light",
    description: "Always uses the light theme.",
    id: "light"
  },
  {
    label: "Dark",
    description: "Always uses the dark theme.",
    id: "dark"
  }
])
const value = ref("light")
</script>

<template>
  <URadioGroup v-model="value" value-key="id" :items="items" />
</template>
```

### Legend

Use the `legend` prop to set the legend of the RadioGroup.

```vue
<script setup lang="ts">
const items = ref([
  "System",
  "Light",
  "Dark"
])
</script>

<template>
  <URadioGroup legend="Theme" default-value="System" :items="items" />
</template>
```

### Color

Use the `color` prop to change the color of the RadioGroup.

```vue
<script setup lang="ts">
const items = ref([
  "System",
  "Light",
  "Dark"
])
</script>

<template>
  <URadioGroup color="neutral" default-value="System" :items="items" />
</template>
```

### Variant

Use the `variant` prop to change the variant of the RadioGroup.

```vue
<script setup lang="ts">
import type { RadioGroupItem } from '@nuxt/ui'

const items = ref<RadioGroupItem[]>([
  {
    label: "System",
    value: "system",
    description: "Matches your device settings."
  },
  {
    label: "Light",
    value: "light",
    description: "Always uses the light theme."
  },
  {
    label: "Dark",
    value: "dark",
    description: "Always uses the dark theme."
  }
])
</script>

<template>
  <URadioGroup color="primary" variant="card" default-value="system" :items="items" />
</template>
```

### Size

Use the `size` prop to change the size of the RadioGroup.

```vue
<script setup lang="ts">
const items = ref([
  "System",
  "Light",
  "Dark"
])
</script>

<template>
  <URadioGroup size="xl" variant="list" default-value="System" :items="items" />
</template>
```

### Orientation

Use the `orientation` prop to change the orientation of the RadioGroup. Defaults to `vertical`.

```vue
<script setup lang="ts">
const items = ref([
  "System",
  "Light",
  "Dark"
])
</script>

<template>
  <URadioGroup orientation="horizontal" variant="list" default-value="System" :items="items" />
</template>
```

### Indicator

Use the `indicator` prop to change the position or hide the indicator. Defaults to `start`.

> [!NOTE]
> 
> An item's `icon` is only displayed when `indicator` is `hidden`, above the label, since a radio has no icon inside its indicator.

```vue
<script setup lang="ts">
import type { RadioGroupItem } from '@nuxt/ui'

const items = ref<RadioGroupItem[]>([
  {
    label: "System",
    icon: "i-lucide-monitor",
    value: "System",
    class: "w-20"
  },
  {
    label: "Light",
    icon: "i-lucide-sun",
    value: "Light",
    class: "w-20"
  },
  {
    label: "Dark",
    icon: "i-lucide-moon",
    value: "Dark",
    class: "w-20"
  }
])
</script>

<template>
  <URadioGroup indicator="hidden" orientation="horizontal" variant="table" default-value="System" :items="items" />
</template>
```

### Disabled

Use the `disabled` prop to disable the RadioGroup.

```vue
<script setup lang="ts">
const items = ref([
  "System",
  "Light",
  "Dark"
])
</script>

<template>
  <URadioGroup disabled default-value="System" :items="items" />
</template>
```

## API

### Props

```ts
/**
 * Props for the RadioGroup component
 */
interface RadioGroupProps {
  /**
   * The element or component this component should render as.
   * @default 'div'
   */
  as?: any;
  legend?: string | undefined;
  /**
   * When `items` is an array of objects, select the field to use as the value.
   * @default 'value'
   */
  valueKey?: VK | undefined;
  /**
   * When `items` is an array of objects, select the field to use as the label.
   * @default 'label'
   */
  labelKey?: keyof Extract<NestedItem<T>, object> & string | DotPathKeys<Extract<NestedItem<T>, object>> | undefined;
  /**
   * When `items` is an array of objects, select the field to use as the description.
   * @default 'description'
   */
  descriptionKey?: keyof Extract<NestedItem<T>, object> & string | DotPathKeys<Extract<NestedItem<T>, object>> | undefined;
  items?: T | undefined;
  /**
   * The controlled value of the RadioGroup. Can be bind as `v-model`.
   */
  modelValue?: GetItemValue<T, VK, undefined, NestedItem<T>> | undefined;
  /**
   * The value of the RadioGroup when initially rendered. Use when you do not need to control the state of the RadioGroup.
   */
  defaultValue?: GetItemValue<T, VK, undefined, NestedItem<T>> | undefined;
  /**
   * @default 'md'
   */
  size?: "xs" | "sm" | "md" | "lg" | "xl" | undefined;
  /**
   * @default 'list'
   */
  variant?: "card" | "list" | "table" | undefined;
  /**
   * @default 'primary'
   */
  color?: "primary" | "secondary" | "success" | "info" | "warning" | "error" | "neutral" | undefined;
  /**
   * Highlight the ring color like a focus state.
   * @default false
   */
  highlight?: boolean | undefined;
  /**
   * The orientation the radio buttons are laid out.
   * @default 'vertical'
   */
  orientation?: "horizontal" | "vertical" | undefined;
  /**
   * Position of the indicator.
   * @default 'start'
   */
  indicator?: "start" | "end" | "hidden" | undefined;
  ui?: { root?: SlotClass; fieldset?: SlotClass; legend?: SlotClass; item?: SlotClass; container?: SlotClass; base?: SlotClass; indicator?: SlotClass; wrapper?: SlotClass; label?: SlotClass; icon?: SlotClass; description?: SlotClass; } | undefined;
  /**
   * When `true`, prevents the user from interacting with radio items.
   */
  disabled?: boolean | undefined;
  /**
   * When `true`, keyboard navigation will loop from last item to first, and vice versa.
   */
  loop?: boolean | undefined;
  /**
   * The name of the field. Submitted with its owning form as part of a name/value pair.
   */
  name?: string | undefined;
  /**
   * When `true`, indicates that the user must set the value before the owning form can be submitted.
   */
  required?: boolean | undefined;
}
```

### Slots

```ts
/**
 * Slots for the RadioGroup component
 */
interface RadioGroupSlots {
  legend(): any;
  label(): any;
  description(): any;
}
```

### Emits

```ts
/**
 * Emitted events for the RadioGroup component
 */
interface RadioGroupEmits {
  update:modelValue: (payload: [value: GetItemValue<T, VK, undefined, NestedItem<T>>]) => void;
  change: (payload: [event: Event]) => void;
}
```

## Theme

```ts [app.config.ts]
export default defineAppConfig({
  ui: {
    radioGroup: {
      slots: {
        root: 'relative',
        fieldset: 'flex gap-x-2',
        legend: 'mb-1 block font-medium text-default',
        item: 'flex items-start',
        container: 'flex items-center',
        base: 'rounded-full ring ring-inset ring-accented overflow-hidden focus-visible:outline-none',
        indicator: 'flex items-center justify-center size-full after:bg-default after:rounded-full',
        wrapper: 'w-full',
        label: 'block font-medium text-default',
        icon: 'shrink-0',
        description: 'text-muted'
      },
      variants: {
        color: {
          primary: {
            indicator: 'bg-primary'
          },
          secondary: {
            indicator: 'bg-secondary'
          },
          success: {
            indicator: 'bg-success'
          },
          info: {
            indicator: 'bg-info'
          },
          warning: {
            indicator: 'bg-warning'
          },
          error: {
            indicator: 'bg-error'
          },
          neutral: {
            indicator: 'bg-inverted'
          }
        },
        variant: {
          list: {
            fieldset: 'flex-wrap',
            item: ''
          },
          card: {
            fieldset: 'flex-wrap',
            item: [
              'border border-default rounded-lg hover:not-has-disabled:not-has-focus-visible:not-has-data-[state=checked]:bg-elevated/50',
              'transition-colors'
            ]
          },
          table: {
            item: [
              'border border-default hover:not-has-disabled:not-has-focus-visible:not-has-data-[state=checked]:bg-elevated/50',
              'transition-colors'
            ]
          }
        },
        orientation: {
          horizontal: {
            fieldset: 'flex-row'
          },
          vertical: {
            fieldset: 'flex-col'
          }
        },
        indicator: {
          start: {
            item: 'flex-row',
            wrapper: 'ms-2'
          },
          end: {
            item: 'flex-row-reverse',
            wrapper: 'me-2'
          },
          hidden: {
            base: 'sr-only',
            wrapper: 'flex flex-col items-center gap-1 text-center'
          }
        },
        size: {
          xs: {
            fieldset: 'gap-y-0.5',
            legend: 'text-xs',
            base: 'size-3',
            item: 'text-xs',
            container: 'h-4',
            indicator: 'after:size-1'
          },
          sm: {
            fieldset: 'gap-y-0.5',
            legend: 'text-xs',
            base: 'size-3.5',
            item: 'text-xs',
            container: 'h-4',
            indicator: 'after:size-1'
          },
          md: {
            fieldset: 'gap-y-1',
            legend: 'text-sm',
            base: 'size-4',
            item: 'text-sm',
            container: 'h-5',
            indicator: 'after:size-1.5'
          },
          lg: {
            fieldset: 'gap-y-1',
            legend: 'text-sm',
            base: 'size-4.5',
            item: 'text-sm',
            container: 'h-5',
            indicator: 'after:size-1.5'
          },
          xl: {
            fieldset: 'gap-y-1.5',
            legend: 'text-base',
            base: 'size-5',
            item: 'text-base',
            container: 'h-6',
            indicator: 'after:size-2'
          }
        },
        highlight: {
          true: '',
          false: ''
        },
        disabled: {
          true: {
            item: 'opacity-75',
            base: 'cursor-not-allowed',
            label: 'cursor-not-allowed',
            description: 'cursor-not-allowed'
          }
        },
        required: {
          true: {
            legend: "after:content-['*'] after:ms-0.5 after:text-error"
          }
        }
      },
      compoundVariants: [
        {
          indicator: 'hidden',
          class: {
            container: 'h-auto'
          }
        },
        {
          variant: [
            'card',
            'table'
          ],
          highlight: false,
          class: {
            item: 'hover:not-has-disabled:not-has-focus-visible:not-has-data-[state=checked]:border-accented'
          }
        },
        {
          size: 'xs',
          indicator: 'hidden',
          class: {
            icon: 'size-3'
          }
        },
        {
          size: 'sm',
          indicator: 'hidden',
          class: {
            icon: 'size-3.5'
          }
        },
        {
          size: 'md',
          indicator: 'hidden',
          class: {
            icon: 'size-4'
          }
        },
        {
          size: 'lg',
          indicator: 'hidden',
          class: {
            icon: 'size-4.5'
          }
        },
        {
          size: 'xl',
          indicator: 'hidden',
          class: {
            icon: 'size-5'
          }
        },
        {
          size: 'xs',
          variant: [
            'card',
            'table'
          ],
          class: {
            item: 'p-2.5'
          }
        },
        {
          size: 'sm',
          variant: [
            'card',
            'table'
          ],
          class: {
            item: 'p-3'
          }
        },
        {
          size: 'md',
          variant: [
            'card',
            'table'
          ],
          class: {
            item: 'p-3.5'
          }
        },
        {
          size: 'lg',
          variant: [
            'card',
            'table'
          ],
          class: {
            item: 'p-4'
          }
        },
        {
          size: 'xl',
          variant: [
            'card',
            'table'
          ],
          class: {
            item: 'p-4.5'
          }
        },
        {
          orientation: 'horizontal',
          variant: 'table',
          class: {
            item: 'first-of-type:rounded-s-lg last-of-type:rounded-e-lg',
            fieldset: 'gap-0 -space-x-px'
          }
        },
        {
          orientation: 'vertical',
          variant: 'table',
          class: {
            item: 'first-of-type:rounded-t-lg last-of-type:rounded-b-lg',
            fieldset: 'gap-0 -space-y-px'
          }
        },
        {
          color: 'primary',
          variant: 'list',
          indicator: [
            'start',
            'end'
          ],
          class: {
            base: 'outline-primary/25 focus-visible:outline-solid focus-visible:outline-3 focus-visible:ring-primary'
          }
        },
        {
          color: 'secondary',
          variant: 'list',
          indicator: [
            'start',
            'end'
          ],
          class: {
            base: 'outline-secondary/25 focus-visible:outline-solid focus-visible:outline-3 focus-visible:ring-secondary'
          }
        },
        {
          color: 'success',
          variant: 'list',
          indicator: [
            'start',
            'end'
          ],
          class: {
            base: 'outline-success/25 focus-visible:outline-solid focus-visible:outline-3 focus-visible:ring-success'
          }
        },
        {
          color: 'info',
          variant: 'list',
          indicator: [
            'start',
            'end'
          ],
          class: {
            base: 'outline-info/25 focus-visible:outline-solid focus-visible:outline-3 focus-visible:ring-info'
          }
        },
        {
          color: 'warning',
          variant: 'list',
          indicator: [
            'start',
            'end'
          ],
          class: {
            base: 'outline-warning/25 focus-visible:outline-solid focus-visible:outline-3 focus-visible:ring-warning'
          }
        },
        {
          color: 'error',
          variant: 'list',
          indicator: [
            'start',
            'end'
          ],
          class: {
            base: 'outline-error/25 focus-visible:outline-solid focus-visible:outline-3 focus-visible:ring-error'
          }
        },
        {
          color: 'neutral',
          variant: 'list',
          indicator: [
            'start',
            'end'
          ],
          class: {
            base: 'outline-inverted/25 focus-visible:outline-solid focus-visible:outline-3 focus-visible:ring-inverted'
          }
        },
        {
          color: 'primary',
          variant: [
            'card',
            'table'
          ],
          class: {
            item: 'outline-primary/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-primary has-focus-visible:z-[1]'
          }
        },
        {
          color: 'secondary',
          variant: [
            'card',
            'table'
          ],
          class: {
            item: 'outline-secondary/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-secondary has-focus-visible:z-[1]'
          }
        },
        {
          color: 'success',
          variant: [
            'card',
            'table'
          ],
          class: {
            item: 'outline-success/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-success has-focus-visible:z-[1]'
          }
        },
        {
          color: 'info',
          variant: [
            'card',
            'table'
          ],
          class: {
            item: 'outline-info/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-info has-focus-visible:z-[1]'
          }
        },
        {
          color: 'warning',
          variant: [
            'card',
            'table'
          ],
          class: {
            item: 'outline-warning/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-warning has-focus-visible:z-[1]'
          }
        },
        {
          color: 'error',
          variant: [
            'card',
            'table'
          ],
          class: {
            item: 'outline-error/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-error has-focus-visible:z-[1]'
          }
        },
        {
          color: 'neutral',
          variant: [
            'card',
            'table'
          ],
          class: {
            item: 'outline-inverted/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-inverted has-focus-visible:z-[1]'
          }
        },
        {
          color: 'primary',
          variant: 'list',
          indicator: 'hidden',
          class: {
            item: 'outline-primary/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-primary has-focus-visible:z-[1]'
          }
        },
        {
          color: 'secondary',
          variant: 'list',
          indicator: 'hidden',
          class: {
            item: 'outline-secondary/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-secondary has-focus-visible:z-[1]'
          }
        },
        {
          color: 'success',
          variant: 'list',
          indicator: 'hidden',
          class: {
            item: 'outline-success/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-success has-focus-visible:z-[1]'
          }
        },
        {
          color: 'info',
          variant: 'list',
          indicator: 'hidden',
          class: {
            item: 'outline-info/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-info has-focus-visible:z-[1]'
          }
        },
        {
          color: 'warning',
          variant: 'list',
          indicator: 'hidden',
          class: {
            item: 'outline-warning/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-warning has-focus-visible:z-[1]'
          }
        },
        {
          color: 'error',
          variant: 'list',
          indicator: 'hidden',
          class: {
            item: 'outline-error/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-error has-focus-visible:z-[1]'
          }
        },
        {
          color: 'neutral',
          variant: 'list',
          indicator: 'hidden',
          class: {
            item: 'outline-inverted/25 has-focus-visible:outline-3 not-has-disabled:has-focus-visible:border-inverted has-focus-visible:z-[1]'
          }
        },
        {
          color: 'primary',
          variant: 'card',
          class: {
            item: 'has-data-[state=checked]:border-primary/50 has-data-[state=checked]:bg-primary/10'
          }
        },
        {
          color: 'secondary',
          variant: 'card',
          class: {
            item: 'has-data-[state=checked]:border-secondary/50 has-data-[state=checked]:bg-secondary/10'
          }
        },
        {
          color: 'success',
          variant: 'card',
          class: {
            item: 'has-data-[state=checked]:border-success/50 has-data-[state=checked]:bg-success/10'
          }
        },
        {
          color: 'info',
          variant: 'card',
          class: {
            item: 'has-data-[state=checked]:border-info/50 has-data-[state=checked]:bg-info/10'
          }
        },
        {
          color: 'warning',
          variant: 'card',
          class: {
            item: 'has-data-[state=checked]:border-warning/50 has-data-[state=checked]:bg-warning/10'
          }
        },
        {
          color: 'error',
          variant: 'card',
          class: {
            item: 'has-data-[state=checked]:border-error/50 has-data-[state=checked]:bg-error/10'
          }
        },
        {
          color: 'neutral',
          variant: 'card',
          class: {
            item: 'has-data-[state=checked]:border-inverted/50 has-data-[state=checked]:bg-elevated'
          }
        },
        {
          color: 'primary',
          variant: 'table',
          class: {
            item: 'has-data-[state=checked]:bg-primary/10 has-data-[state=checked]:border-primary/50 has-data-[state=checked]:z-[1]'
          }
        },
        {
          color: 'secondary',
          variant: 'table',
          class: {
            item: 'has-data-[state=checked]:bg-secondary/10 has-data-[state=checked]:border-secondary/50 has-data-[state=checked]:z-[1]'
          }
        },
        {
          color: 'success',
          variant: 'table',
          class: {
            item: 'has-data-[state=checked]:bg-success/10 has-data-[state=checked]:border-success/50 has-data-[state=checked]:z-[1]'
          }
        },
        {
          color: 'info',
          variant: 'table',
          class: {
            item: 'has-data-[state=checked]:bg-info/10 has-data-[state=checked]:border-info/50 has-data-[state=checked]:z-[1]'
          }
        },
        {
          color: 'warning',
          variant: 'table',
          class: {
            item: 'has-data-[state=checked]:bg-warning/10 has-data-[state=checked]:border-warning/50 has-data-[state=checked]:z-[1]'
          }
        },
        {
          color: 'error',
          variant: 'table',
          class: {
            item: 'has-data-[state=checked]:bg-error/10 has-data-[state=checked]:border-error/50 has-data-[state=checked]:z-[1]'
          }
        },
        {
          color: 'neutral',
          variant: 'table',
          class: {
            item: 'has-data-[state=checked]:bg-elevated has-data-[state=checked]:border-inverted/50 has-data-[state=checked]:z-[1]'
          }
        },
        {
          variant: [
            'card',
            'table'
          ],
          disabled: true,
          class: {
            item: 'cursor-not-allowed'
          }
        },
        {
          color: 'primary',
          indicator: 'hidden',
          highlight: true,
          class: {
            item: 'not-has-disabled:border-primary not-has-disabled:has-data-[state=checked]:border-primary'
          }
        },
        {
          color: 'secondary',
          indicator: 'hidden',
          highlight: true,
          class: {
            item: 'not-has-disabled:border-secondary not-has-disabled:has-data-[state=checked]:border-secondary'
          }
        },
        {
          color: 'success',
          indicator: 'hidden',
          highlight: true,
          class: {
            item: 'not-has-disabled:border-success not-has-disabled:has-data-[state=checked]:border-success'
          }
        },
        {
          color: 'info',
          indicator: 'hidden',
          highlight: true,
          class: {
            item: 'not-has-disabled:border-info not-has-disabled:has-data-[state=checked]:border-info'
          }
        },
        {
          color: 'warning',
          indicator: 'hidden',
          highlight: true,
          class: {
            item: 'not-has-disabled:border-warning not-has-disabled:has-data-[state=checked]:border-warning'
          }
        },
        {
          color: 'error',
          indicator: 'hidden',
          highlight: true,
          class: {
            item: 'not-has-disabled:border-error not-has-disabled:has-data-[state=checked]:border-error'
          }
        },
        {
          color: 'neutral',
          indicator: 'hidden',
          highlight: true,
          class: {
            item: 'not-has-disabled:border-inverted not-has-disabled:has-data-[state=checked]:border-inverted'
          }
        },
        {
          color: 'primary',
          highlight: true,
          class: {
            base: 'ring-primary'
          }
        },
        {
          color: 'secondary',
          highlight: true,
          class: {
            base: 'ring-secondary'
          }
        },
        {
          color: 'success',
          highlight: true,
          class: {
            base: 'ring-success'
          }
        },
        {
          color: 'info',
          highlight: true,
          class: {
            base: 'ring-info'
          }
        },
        {
          color: 'warning',
          highlight: true,
          class: {
            base: 'ring-warning'
          }
        },
        {
          color: 'error',
          highlight: true,
          class: {
            base: 'ring-error'
          }
        },
        {
          color: 'neutral',
          highlight: true,
          class: {
            base: 'ring-inverted'
          }
        }
      ],
      defaultVariants: {
        highlight: false,
        size: 'md',
        color: 'primary',
        variant: 'list',
        indicator: 'start'
      }
    }
  }
})
```

## Changelog

See commit history for [component](https://github.com/nuxt/ui/commits/v4/src/runtime/components/RadioGroup.vue) and [theme](https://github.com/nuxt/ui/commits/v4/src/theme/radio-group.ts).

---

- [RadioGroup](https://reka-ui.com/docs/components/radio-group)
- [GitHub](https://github.com/nuxt/ui/blob/v4/src/runtime/components/RadioGroup.vue)


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
