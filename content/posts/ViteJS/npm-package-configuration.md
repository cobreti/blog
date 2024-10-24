---
title: ViteJS NPM package configuration
description: Example of a ViteJS NPM package configuration
publishDate: 2024-10-19
tags: ['vitejs', 'npm', 'configuration']
---

### vite.config.ts

```typescript
import { defineConfig } from "vitest/config";
import dts from "vite-plugin-dts";

export default defineConfig({
  test: {
    environment: 'jsdom',
    coverage: {
      provider: 'istanbul'
    }
  },
  build: {
    lib: {
      entry: "src/index.ts",
      formats: ["es", "cjs"],
      name: "index",
      fileName: (format) => `index.${format}.js`,
    },
  },
  plugins: [dts()]
});
```

### tsconfig.json

```typescript
{
  "compilerOptions": {
    "target": "ESNext",
    "lib": ["DOM", "DOM.Iterable", "ESNext"],
    "module": "ESNext",
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",

    /* Output */
    "outDir": "./dist",
    "declaration": true,

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}

// tsconfig.node.json
{
  "compilerOptions": {
    "composite": true,
    "skipLibCheck": true,
    "module": "ESNext",
    "moduleResolution": "bundler",
    "allowSyntheticDefaultImports": true
  },
  "include": ["vite.config.ts"]
}
```

### package.json

```typescript
{
  "name": "@cobreti/web-component",
  "version": "0.0.2",
  "license": "MIT",
  "keywords": [
    "Web Component"
  ],
  "type": "module",
  "main": "./dist/index.cjs.js",
  "module": "./dist/index.es.js",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "require": "./dist/index.cjs.js",
      "import": "./dist/index.es.js",
      "types": "./dist/index.d.ts"
    },
    "./package.json": "./package.json"
  },
  "files": [
    "dist",
    "src"
  ],
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "lint": "eslint src --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "preview": "vite preview",
    "test": "vitest",
    "coverage": "vitest run --coverage"
  },
  "devDependencies": {
    "@types/node": "^20.8.6",
    "@types/jsdom": "^21.1.3",
    "@typescript-eslint/eslint-plugin": "^5.57.1",
    "@typescript-eslint/parser": "^5.57.1",
    "@vitest/coverage-istanbul": "^0.34.6",
    "eslint": "^8.38.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.3.4",
    "jsdom": "^22.1.0",
    "typescript": "^5.0.2",
    "vite": "^4.3.2",
    "vite-plugin-dts": "^2.3.0",
    "vitest": "^0.34.6"
  }
}
```
