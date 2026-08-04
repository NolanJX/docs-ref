# nuxt dev

> The dev command starts a development server with hot module replacement at http://localhost:3000

```bash [Terminal]
npx nuxt dev [ROOTDIR] [--cwd=<directory>] [--logLevel=<silent|info|verbose>] [--dotenv] [--envName] [-e, --extends=<layer-name>] [--clear] [--no-f, --no-fork] [-p, --port] [-h, --host] [--clipboard] [-o, --open] [--https] [--publicURL] [--qr] [--public] [--tunnel] [--profile[=verbose]] [--sslCert] [--sslKey]
```

The `dev` command starts a development server with hot module replacement at [http://localhost:3000](https://localhost:3000)

## Arguments

<table>
<thead>
  <tr>
    <th>
      Argument
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
        ROOTDIR="."
      </code>
    </td>
    
    <td>
      Specifies the working directory (default: <code>
        .
      </code>
      
      )
    </td>
  </tr>
</tbody>
</table>

## Options

<table>
<thead>
  <tr>
    <th>
      Option
    </th>
    
    <th>
      Default
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
        --cwd=<directory>
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Specify the working directory, this takes precedence over ROOTDIR (default: <code>
        .
      </code>
      
      )
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --logLevel=<silent|info|verbose>
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Specify build-time log level
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --dotenv
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Path to <code>
        .env
      </code>
      
       file to load, relative to the root directory
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --envName
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      The environment to use when resolving configuration overrides (default is <code>
        production
      </code>
      
       when building, and <code>
        development
      </code>
      
       when running the dev server)
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        -e, --extends=<layer-name>
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Extend from a Nuxt layer
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --clear
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      Clear console on restart
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --no-f, --no-fork
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Disable forked mode
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        -p, --port
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Port to listen on (default: <code>
        NUXT_PORT || NITRO_PORT || PORT || nuxtOptions.devServer.port
      </code>
      
      )
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        -h, --host
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Host to listen on (default: <code>
        NUXT_HOST || NITRO_HOST || HOST || nuxtOptions.devServer?.host
      </code>
      
      )
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --clipboard
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      Copy the URL to the clipboard
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        -o, --open
      </code>
    </td>
    
    <td>
      <code>
        false
      </code>
    </td>
    
    <td>
      Open the URL in the browser
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --https
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Enable HTTPS
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --publicURL
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Displayed public URL (used for QR code)
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --qr
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Display The QR code of public URL when available
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --public
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Listen to all network interfaces
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --tunnel
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Open a tunnel using <a href="https://github.com/unjs/untun" rel="nofollow">
        https://github.com/unjs/untun
      </a>
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --profile
      </code>
      
       <badge className="align-middle" color="info" size="xs">
        v4.4
      </badge>
    </td>
    
    <td>
      
    </td>
    
    <td>
      Profile performance. Writes a V8 CPU profile and JSON report on exit. Use <code>
        --profile=verbose
      </code>
      
       for a full console report.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --sslCert
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      (DEPRECATED) Use <code>
        --https.cert
      </code>
      
       instead.
    </td>
  </tr>
  
  <tr>
    <td>
      <code>
        --sslKey
      </code>
    </td>
    
    <td>
      
    </td>
    
    <td>
      (DEPRECATED) Use <code>
        --https.key
      </code>
      
       instead.
    </td>
  </tr>
</tbody>
</table>

The port and host can also be set via NUXT_PORT, PORT, NUXT_HOST or HOST environment variables.

Additionally to the above options, `@nuxt/cli` can pass options through to `listhen`, e.g. `--no-qr` to turn off the dev server QR code. You can find the list of `listhen` options in the [unjs/listhen](https://github.com/unjs/listhen) docs.

This command sets `process.env.NODE_ENV` to `development`.

<note>

If you are using a self-signed certificate in development, you will need to set `NODE_TLS_REJECT_UNAUTHORIZED=0` in your environment.

</note>

<style>

html pre.shiki code .s52Pk, html code.shiki .s52Pk{--shiki-light:#E2931D;--shiki-default:#E2931D;--shiki-dark:#FFCB6B}html pre.shiki code .sGFVr, html code.shiki .sGFVr{--shiki-light:#91B859;--shiki-default:#91B859;--shiki-dark:#C3E88D}html pre.shiki code .sZSNi, html code.shiki .sZSNi{--shiki-light:#90A4AE;--shiki-default:#90A4AE;--shiki-dark:#BABED8}html pre.shiki code .sDfIl, html code.shiki .sDfIl{--shiki-light:#39ADB5;--shiki-default:#39ADB5;--shiki-dark:#89DDFF}html .light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html.light .shiki span {color: var(--shiki-light);background: var(--shiki-light-bg);font-style: var(--shiki-light-font-style);font-weight: var(--shiki-light-font-weight);text-decoration: var(--shiki-light-text-decoration);}html .default .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .shiki span {color: var(--shiki-default);background: var(--shiki-default-bg);font-style: var(--shiki-default-font-style);font-weight: var(--shiki-default-font-weight);text-decoration: var(--shiki-default-text-decoration);}html .dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}html.dark .shiki span {color: var(--shiki-dark);background: var(--shiki-dark-bg);font-style: var(--shiki-dark-font-style);font-weight: var(--shiki-dark-font-weight);text-decoration: var(--shiki-dark-text-decoration);}

</style>

---

- [Source](https://github.com/nuxt/cli/blob/main/packages/nuxi/src/commands/dev.ts)
