---
title: "useTour"
description: "A composable to build guided tours by re-anchoring a single Popover across steps."
canonical_url: "https://ui.nuxt.com/docs/composables/use-tour"
---
# useTour

> A composable to build guided tours by re-anchoring a single Popover across steps.

## Usage

Use the auto-imported `useTour` composable to drive a guided tour with a single [Popover](https://ui.nuxt.com/docs/components/popover) whose anchor moves between steps. The composable owns the step state and resolves each step's `target` into a `reference` you bind to `<UPopover>`, while you keep full control over the content and navigation.

```vue [UseTourExample.vue]
<script setup lang="ts">
const dashboard = useTemplateRef('dashboard')
const profile = useTemplateRef('profile')
const settings = useTemplateRef('settings')

const tour = useTour([
  {
    target: () => dashboard.value,
    title: 'Welcome aboard',
    body: 'The popover re-anchors to each target as you step through the tour.'
  },
  {
    target: () => profile.value,
    title: 'Make it yours',
    body: 'You own the content and the buttons — no extra theme or locale to maintain.',
    side: 'right' as const
  },
  {
    target: () => settings.value,
    title: 'You\'re all set',
    body: 'Press Finish to close the tour.'
  }
])
</script>

<template>
  <div class="w-full space-y-4">
    <div class="flex justify-end">
      <UButton icon="i-lucide-wand-sparkles" @click="tour.start()">
        Start tour
      </UButton>
    </div>

    <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
      <div ref="dashboard" class="p-4 rounded-lg border border-default bg-elevated/50">
        <p class="font-medium text-highlighted">
          Dashboard
        </p>
        <p class="text-sm text-muted">
          Overview of your workspace.
        </p>
      </div>
      <div ref="profile" class="p-4 rounded-lg border border-default bg-elevated/50">
        <p class="font-medium text-highlighted">
          Profile
        </p>
        <p class="text-sm text-muted">
          Manage your account.
        </p>
      </div>
      <div ref="settings" class="p-4 rounded-lg border border-default bg-elevated/50">
        <p class="font-medium text-highlighted">
          Settings
        </p>
        <p class="text-sm text-muted">
          Configure your preferences.
        </p>
      </div>
    </div>

    <UPopover
      :open="tour.open.value"
      :reference="tour.reference.value"
      :content="{ side: tour.current.value?.side, sideOffset: 8 }"
      :dismissible="false"
      arrow
    >
      <template #content>
        <div class="p-4 max-w-xs space-y-2">
          <div class="flex items-center justify-between gap-4">
            <p class="font-semibold text-highlighted">
              {{ tour.current.value?.title }}
            </p>
            <span class="text-xs text-muted tabular-nums">
              {{ tour.index.value + 1 }} / {{ tour.total.value }}
            </span>
          </div>
          <p class="text-sm text-muted">
            {{ tour.current.value?.body }}
          </p>
          <div class="flex items-center justify-between pt-2">
            <UButton
              color="neutral"
              variant="outline"
              size="sm"
              :disabled="!tour.hasPrev.value"
              @click="tour.prev()"
            >
              Back
            </UButton>
            <UButton size="sm" @click="tour.next()">
              {{ tour.hasNext.value ? 'Next' : 'Finish' }}
            </UButton>
          </div>
        </div>
      </template>
    </UPopover>
  </div>
</template>
```

Each step requires a `target` that the popover anchors to. It accepts a CSS selector, an element, a virtual element (anything with `getBoundingClientRect`), or a ref/getter returning one of those. Pass `null` to anchor the step to the center of the viewport. Any other field on a step (`title`, `body`, `side`, …) is passed through untouched and available via `current`.

```vue
<script setup lang="ts">
const card = useTemplateRef('card')

const tour = useTour([
  { target: '#cta', title: 'Get started' },
  { target: () => card.value, title: 'Profile', side: 'right' },
  { target: null, title: 'All set' }
])
</script>

<template>
  <UButton @click="tour.start()">Start tour</UButton>

  <UPopover :open="tour.open.value" :reference="tour.reference.value" :dismissible="false">
    <template #content>
      <!-- your content + buttons -->
      <UButton :disabled="!tour.hasPrev.value" @click="tour.prev()">Back</UButton>
      <UButton @click="tour.next()">{{ tour.hasNext.value ? 'Next' : 'Finish' }}</UButton>
    </template>
  </UPopover>
</template>
```

- Built on the Popover's reactive `reference` prop, so the popover smoothly repositions when the active step changes.
- The active target is scrolled into view automatically when a step becomes active.
- Since you render the content yourself, there is no extra theme or locale to maintain.

## API

`useTour(steps, options?)`

### Parameters

**steps** (`MaybeRefOrGetter<TourStep[]>`) *required*: The list of tour steps. Can be a static array, a ref, or a getter for reactive steps.The element the step anchors to. Accepts a CSS selector ('#id', '.class', or a bare id resolved as #id), an element, a virtual element, or a ref/getter returning one. Use null to center the step in the viewport.Any additional fields (title, body, side, …) are passed through and available via current.

**options** (`UseTourOptions`): Configuration options for the tour.The step index the tour starts on.Loop back to the first step after the last one.Scroll the target into view when a step becomes active.

### Return

**open** (`Ref<boolean>`): Whether the tour is currently open.

**index** (`Ref<number>`): The current step index, clamped to the steps range.

**current** (`ComputedRef<TourStep | undefined>`): The current step object, or undefined when there are no steps.

**reference** (`ComputedRef<ReferenceElement | undefined>`): The resolved anchor for the current step, to pass to <UPopover :reference>.

**total** (`ComputedRef<number>`): The total number of steps.

**hasNext** (`ComputedRef<boolean>`): Whether a next step exists.

**hasPrev** (`ComputedRef<boolean>`): Whether a previous step exists.

**start** (`(index?: number) => void`): Open the tour, optionally at a given index.

**next** (`() => void`): Go to the next step. Loops or finishes at the end depending on the loop option.

**prev** (`() => void`): Go to the previous step.

**goTo** (`(index: number) => void`): Jump to a specific step and open the tour.

**finish** (`() => void`): Close the tour.


## Sitemap

See the full [sitemap](https://ui.nuxt.com/sitemap.md) for all pages.
