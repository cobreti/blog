---
title: ViteJS Application Configuration
description: Example of ViteJS Application configuration
publishDate: 2024-10-19
tags: ['vitejs', 'configuration', 'application']
---

### vite.config.ts

```typescript
import {defineConfig} from 'vitest/config'
import sassDts from 'vite-plugin-sass-dts'
import {resolve} from "path";

export default defineConfig({
    test: {
        coverage: {
            provider: 'istanbul'
        }
    },
    server: {
        port: 8102,
        host: "0.0.0.0"
    },
    plugins: [
        sassDts()
    ],
    resolve: {
        alias: {
            '@interfaces': resolve(__dirname, './src/interfaces'),
        }
    }
});
```

### tsconfig.json

```typescript
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,

    /* Linting */
    "strict": true,
    "noUnusedLocals": false,
    "noUnusedParameters": false,
    "noFallthroughCasesInSwitch": true,

    "baseUrl": "./src",
    "paths": {
      "@interfaces/*": ["interfaces/*"]
    }
  },
  "include": ["src"]
}
```

### package.json

```typescript
{
  "name": "testapp",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "coverage": "vitest run --coverage"
  },
  "devDependencies": {
    "@vitest/coverage-istanbul": "^0.34.6",
    "typescript": "^5.0.2",
    "vite": "^4.4.5",
    "vite-plugin-sass-dts": "^1.3.11",
    "vitest": "^0.34.6"
  },
  "dependencies": {
    "@cobreti/web-component": "^0.0.2",
    "path": "^0.12.7",
    "rxjs": "^7.8.1"
  }
}
```
