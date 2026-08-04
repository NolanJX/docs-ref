# Compatibility

> Nuxt Kit provides a set of utilities to help you check the compatibility of your modules with different Nuxt versions.

Nuxt Kit utilities can be used in Nuxt 3, Nuxt 2 with Bridge and even Nuxt 2 without Bridge. To make sure your module is compatible with all versions, you can use the `checkNuxtCompatibility`, `assertNuxtCompatibility` and `hasNuxtCompatibility` functions. They will check if the current Nuxt version meets the constraints you provide. Also you can use `isNuxt2`, `isNuxt3` and `getNuxtVersion` functions for more granular checks.

## `checkNuxtCompatibility`

Checks if constraints are met for the current Nuxt version. If not, returns an array of messages. Nuxt 2 version also checks for `bridge` support.

### Usage

```tstwoslash
import { checkNuxtCompatibility, defineNuxtModule } from '@nuxt/kit'

export default defineNuxtModule({
  async setup (_options, nuxt) {
    const issues = await checkNuxtCompatibility({ nuxt: '^2.16.0' }, nuxt)
    if (issues.length) {
      console.warn('Nuxt compatibility issues found:\n' + issues.toString())
    } else {
      // do something
    }
  },
})
```

### Type

```ts
function checkNuxtCompatibility (constraints: NuxtCompatibility, nuxt?: Nuxt): Promise<NuxtCompatibilityIssues>
```

### Parameters

**constraints**: Version and builder constraints to check against. It accepts the following properties:

<table>
<thead>
  <tr>
    <th>
      Property
    </th>
    
    <th>
      Type
    </th>
    
    <th>
      Required
    </th>
    
    <th>
      Description
    </th>
  </tr>
</thead>

<tbody>
  <tr>
    <td>
      <code>
        nuxt
      </code>
    </td>
    
    <td>
      <code>
        string
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      Nuxt version in semver format. Versions may be defined in Node.js way, for example: <code>
        >=2.15.0 <3.0.0
      </code>
      
      .
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        bridge
      </code>
    </td>
    
    <td>
      <code className="language-ts shiki shiki-themes material-theme-lighter material-theme-lighter material-theme-palenight" language="ts" style="">
        <span class="sZSNi">
          Record
        </span>
        
        <span class="sDfIl">
          <
        </span>
        
        <span class="sZSNi">
          string
        </span>
        
        <span class="sDfIl">
          ,
        </span>
        
        <span class="sZSNi">
          string
        </span>
        
        <span class="sDfIl">
          |
        </span>
        
        <span class="sbKd-">
          false
        </span>
        
        <span class="sDfIl">
          >
        </span>
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      Specifies version constraints or disables compatibility for specific Nuxt builders like <code>
        vite
      </code>
      
      , <code>
        webpack
      </code>
      
      , or <code>
        rspack
      </code>
      
      . Use <code>
        false
      </code>
      
       to disable.
    </td>
  </tr>
</tbody>
</table>

**nuxt**: Nuxt instance. If not provided, it will be retrieved from the context via `useNuxt()` call.

## `assertNuxtCompatibility`

Asserts that constraints are met for the current Nuxt version. If not, throws an error with the list of issues as string.

### Type

```tstwoslash
// @errors: 2391
import type { Nuxt, NuxtCompatibility } from '@nuxt/schema'
// ---cut---
function assertNuxtCompatibility (constraints: NuxtCompatibility, nuxt?: Nuxt): Promise<true>
```

### Parameters

**constraints**: Version and builder constraints to check against. Refer to the [constraints table in `checkNuxtCompatibility`](/docs/4.x/api/kit/compatibility#parameters) for details.

**nuxt**: Nuxt instance. If not provided, it will be retrieved from the context via `useNuxt()` call.

## `hasNuxtCompatibility`

Checks if constraints are met for the current Nuxt version. Return `true` if all constraints are met, otherwise returns `false`. Nuxt 2 version also checks for `bridge` support.

### Usage

```tstwoslash
import { defineNuxtModule, hasNuxtCompatibility } from '@nuxt/kit'

export default defineNuxtModule({
  async setup (_options, nuxt) {
    const usingNewPostcss = await hasNuxtCompatibility({ nuxt: '^2.16.0' }, nuxt)
    if (usingNewPostcss) {
      // do something
    } else {
      // do something else
    }
  },
})
```

### Type

```ts
function hasNuxtCompatibility (constraints: NuxtCompatibility, nuxt?: Nuxt): Promise<boolean>
```

### Parameters

**constraints**: Version and builder constraints to check against. Refer to the [constraints table in `checkNuxtCompatibility`](/docs/4.x/api/kit/compatibility#parameters) for details.

**nuxt**: Nuxt instance. If not provided, it will be retrieved from the context via `useNuxt()` call.

## `isNuxtMajorVersion`

Check if current Nuxt instance is of specified major version

### Usage

```tstwoslash
import { defineNuxtModule, isNuxtMajorVersion } from '@nuxt/kit'

export default defineNuxtModule({
  setup () {
    if (isNuxtMajorVersion(3)) {
      // do something for Nuxt 3
    } else {
      // do something else for other versions
    }
  },
})
```

### Type

```ts
function isNuxtMajorVersion (major: number, nuxt?: Nuxt): boolean
```

### Parameters

**major**: Major version to check against.

**nuxt**: Nuxt instance. If not provided, it will be retrieved from the context via `useNuxt()` call.

## `isNuxt3`

Checks if the current Nuxt version is 3.x.

<note>

Use `isNuxtMajorVersion(2, nuxt)` instead. This may be removed in @nuxt/kit v5 or a future major version.

</note>

### Type

```ts
function isNuxt3 (nuxt?: Nuxt): boolean
```

### Parameters

**nuxt**: Nuxt instance. If not provided, it will be retrieved from the context via `useNuxt()` call.

## `isNuxt2`

Checks if the current Nuxt version is 2.x.

<note>

Use `isNuxtMajorVersion(2, nuxt)` instead. This may be removed in @nuxt/kit v5 or a future major version.

</note>

### Type

```ts
function isNuxt2 (nuxt?: Nuxt): boolean
```

### Parameters

**nuxt**: Nuxt instance. If not provided, it will be retrieved from the context via `useNuxt()` call.

## `getNuxtVersion`

Returns the current Nuxt version.

### Type

```ts
function getNuxtVersion (nuxt?: Nuxt): string
```

### Parameters

**nuxt**: Nuxt instance. If not provided, it will be retrieved from the context via `useNuxt()` call.

<style>

html pre.shiki code .s8R28, html code.shiki .s8R28{--shiki-light:#39ADB5;--shiki-light-font-style:italic;--shiki-default:#39ADB5;--shiki-default-font-style:italic;--shiki-dark:#89DDFF;--shiki-dark-font-style:italic}html pre.shiki code .sDfIl, html code.shiki .sDfIl{--shiki-light:#39ADB5;--shiki-default:#39ADB5;--shiki-dark:#89DDFF}html pre.shiki code .sZSNi, html code.shiki .sZSNi{--shiki-light:#90A4AE;--shiki-default:#90A4AE;--shiki-dark:#BABED8}html pre.shiki code .sGFVr, html code.shiki .sGFVr{--shiki-light:#91B859;--shiki-default:#91B859;--shiki-dark:#C3E88D}html pre.shiki code .s3cPz, html code.shiki .s3cPz{--shiki-light:#6182B8;--shiki-default:#6182B8;--shiki-dark:#82AAFF}html pre.shiki code .smZ93, html code.shiki .smZ93{--shiki-light:#9C3EDA;--shiki-default:#9C3EDA;--shiki-dark:#C792EA}html pre.shiki code .sRlkE, html code.shiki .sRlkE{--shiki-light:#E53935;--shiki-default:#E53935;--shiki-dark:#F07178}html pre.shiki code .s1nJG, html code.shiki .s1nJG{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#BABED8;--shiki-dark-font-style:italic}html pre.shiki code .sWuyu, html code.shiki .sWuyu{--shiki-light:#90A4AE;--shiki-light-font-style:italic;--shiki-default:#90A4AE;--shiki-default-font-style:italic;--shiki-dark:#676E95;--shiki-dark-font-style:italic}html .light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html.light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html .default .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html.dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html pre.shiki code .s52Pk, html code.shiki .s52Pk{--shiki-light:#E2931D;--shiki-default:#E2931D;--shiki-dark:#FFCB6B}html pre.shiki code .sbKd-, html code.shiki .sbKd-{--shiki-light:#FF5370;--shiki-default:#FF5370;--shiki-dark:#FF9CAC}html pre.shiki code .sYRBq, html code.shiki .sYRBq{--shiki-light:#F76D47;--shiki-default:#F76D47;--shiki-dark:#F78C6C}

</style>

---

- [Source](https://github.com/nuxt/nuxt/blob/main/packages/kit/src/compatibility.ts)
