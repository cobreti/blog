---
title: ViteJS Project creation & configuration
description: creating and configuring a new ViteJS project
tags: ['vitejs', 'configuration']
publishDate: 2024-10-15
---

# project creation using ViteJS

A new application can be created easily using the command below. Questions will be asked in the prompt as to what kind of application you want to create and which technology you want to use.

```
npm create vite@latest <app name>
```

# ViteJS Configuration

Here’s an example of my ReactJS configuration.

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react-swc'
import sassDts from 'vite-plugin-sass-dts'
// https://vitejs.dev/config/
export default defineConfig({
  server: {
    port: 8000,
    host: "0.0.0.0"
  },
  plugins: [
      sassDts(),
      react()
  ]
})
```

#### Server section

The server section defines the port and host address to listen to. Here we choose port 8000 and host 0.0.0.0.

Why not using 127.0.0.1 which is the default ? Simply to allow us to use hostname defined in the hosts file instead of only localhost.

For example, if the hosts file contains the following line « 127.0.0.1 demo », using the following url « http://demo:8000/ » would fail if server listen on 127.0.0.1 but succeed if listening on 0.0.0.0.

