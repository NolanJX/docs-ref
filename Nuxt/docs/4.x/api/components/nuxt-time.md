# <NuxtTime>

> The <NuxtTime> component displays time in a locale-friendly format with server-client consistency.

<important>

This component is available in Nuxt v3.17+.

</important>

The `<NuxtTime>` component lets you display dates and times in a locale-friendly format with proper `<time>` HTML semantics. It ensures consistent rendering between server and client without hydration mismatches.

## Usage

You can use the `<NuxtTime>` component anywhere in your app:

```vue [app/app.vue]
<template>
  <NuxtTime :datetime="Date.now()" />
</template>
```

## Props

### `datetime`

- Type: `Date | number | string`
- Required: `true`

The date and time value to display. You can provide:

- A `Date` object
- A timestamp (number)
- An ISO-formatted date string

```vue [app/app.vue]
<template>
  <NuxtTime :datetime="Date.now()" />
  <NuxtTime :datetime="new Date()" />
  <NuxtTime datetime="2023-06-15T09:30:00.000Z" />
</template>
```

### `locale`

- Type: `string`
- Required: `false`
- Default: Uses the browser or server's default locale

The [BCP 47 language tag](https://datatracker.ietf.org/doc/html/rfc5646) for formatting (e.g., 'en-US', 'fr-FR', 'ja-JP'):

```vue [app/app.vue]
<template>
  <NuxtTime
    :datetime="Date.now()"
    locale="fr-FR"
  />
</template>
```

### Formatting Props

The component accepts any property from the [Intl.DateTimeFormat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat/DateTimeFormat) options:

```vue [app/app.vue]
<template>
  <NuxtTime
    :datetime="Date.now()"
    year="numeric"
    month="long"
    day="numeric"
    hour="2-digit"
    minute="2-digit"
  />
</template>
```

This would output something like: "April 22, 2025, 08:30 AM"

### `relative`

- Type: `boolean`
- Required: `false`
- Default: `false`

Enables relative time formatting using the Intl.RelativeTimeFormat API:

```vue [app/app.vue]
<template>
  <!-- Shows something like "5 minutes ago" -->
  <NuxtTime
    :datetime="Date.now() - 5 * 60 * 1000"
    relative
  />
</template>
```

### Relative Time Formatting Props

When `relative` is set to `true`, the component also accepts properties from [Intl.RelativeTimeFormat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/RelativeTimeFormat/RelativeTimeFormat):

<warning>

Due to `style` being a reserved prop, `relativeStyle` prop is used instead.

</warning>

```vue [app/app.vue]
<template>
  <NuxtTime
    :datetime="Date.now() - 3 * 24 * 60 * 60 * 1000"
    relative
    numeric="auto"
    relative-style="long"
  />
</template>
```

This would output something like: "3 days ago" or "last Friday" depending on the `numeric` setting.

## Example

### Basic Usage

```vue [app/app.vue]
<template>
  <NuxtTime :datetime="Date.now()" />
</template>
```

### Custom Formatting

```vue [app/app.vue]
<template>
  <NuxtTime
    :datetime="Date.now()"
    weekday="long"
    year="numeric"
    month="short"
    day="numeric"
    hour="numeric"
    minute="numeric"
    second="numeric"
    time-zone-name="short"
  />
</template>
```

### Relative Time

```vue [app/app.vue]
<template>
  <div>
    <p>
      <NuxtTime
        :datetime="Date.now() - 30 * 1000"
        relative
      />
      <!-- 30 seconds ago -->
    </p>
    <p>
      <NuxtTime
        :datetime="Date.now() - 45 * 60 * 1000"
        relative
      />
      <!-- 45 minutes ago -->
    </p>
    <p>
      <NuxtTime
        :datetime="Date.now() + 2 * 24 * 60 * 60 * 1000"
        relative
      />
      <!-- in 2 days -->
    </p>
  </div>
</template>
```

### With Custom Locale

```vue [app/app.vue]
<template>
  <div>
    <NuxtTime
      :datetime="Date.now()"
      locale="en-US"
      weekday="long"
    />
    <NuxtTime
      :datetime="Date.now()"
      locale="fr-FR"
      weekday="long"
    />
    <NuxtTime
      :datetime="Date.now()"
      locale="ja-JP"
      weekday="long"
    />
  </div>
</template>
```

<style>

html pre.shiki code .sDfIl, html code.shiki .sDfIl{--shiki-light:#39ADB5;--shiki-default:#39ADB5;--shiki-dark:#89DDFF}html pre.shiki code .sRlkE, html code.shiki .sRlkE{--shiki-light:#E53935;--shiki-default:#E53935;--shiki-dark:#F07178}html pre.shiki code .smZ93, html code.shiki .smZ93{--shiki-light:#9C3EDA;--shiki-default:#9C3EDA;--shiki-dark:#C792EA}html pre.shiki code .sGFVr, html code.shiki .sGFVr{--shiki-light:#91B859;--shiki-default:#91B859;--shiki-dark:#C3E88D}html .light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html.light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html .default .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html.dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html pre.shiki code .sWuyu, html code.shiki .sWuyu{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#676E95;--shiki-dark-font-style:italic}

</style>

---

- [Source](https://github.com/nuxt/nuxt/blob/main/packages/nuxt/src/app/components/nuxt-time.vue)
