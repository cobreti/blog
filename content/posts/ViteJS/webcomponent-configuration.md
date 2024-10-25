---
title: ViteJS Web Component configuration
description: Example of ViteJS web-component configuration
publishDate: 2024-10-19
tags: ['vitejs', 'web-component', 'configuration']
---

### vite.config.ts

```typescript
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react-swc'
import sassDts from 'vite-plugin-sass-dts';
import { resolve } from 'path';

// https://vitejs.dev/config/
export default defineConfig({
  test: {
    environment: 'jsdom',
    coverage: {
      provider: 'istanbul'
    },
    setupFiles: './setupTest.ts',
  },
  server: {
    port: 8003,
    host: "0.0.0.0"
  },
  preview: {
    host: '0.0.0.0',
    port: 8003
  },
  plugins: [
    sassDts(),
    react()
  ],
  define: {
    'process.env': {}
  },
  build: {
    sourcemap: true,
    minify: false,
    lib: {
      formats: ['es', 'umd'],
      entry: resolve(__dirname, 'src/index.tsx'),
      name: 'index',
      fileName: (format) => `index.${format}.js`
    },
    watch: {
      buildDelay: 1000
    }
  },
  base: '',
  resolve: {
    alias: {
      '@components': resolve(__dirname, './src/components'),
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
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "declaration": true,
    "sourceMap": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "allowSyntheticDefaultImports": true,
    "jsx": "react-jsx",

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,

    "baseUrl": "./src",
    "paths": {
      "@components/*": ["components/*"]
    }
  },
  "include": [
    "src",
    "./setupTest.ts"
  ],
  "references": [{ "path": "./tsconfig.node.json"}]
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
  "include": [
    "vite.config.ts"
  ]
}
```

### package.json

```typescript
{
  "name": "react",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "lint": "eslint .. --ext ts,tsx --report-unused-disable-directives --max-warnings 0",
    "preview": "vite preview",
    "test": "vitest",
    "coverage": "vitest run --coverage"
  },
  "dependencies": {
    "@types/node": "^20.8.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-hook-form": "^7.47.0",
    "vite-plugin-sass-dts": "^1.3.11"
  },
  "devDependencies": {
    "@cobreti/r2wc": "file:~/dev/npm/cobreti-r2wc-0.0.7.1.tgz",
    "@testing-library/jest-dom": "^6.1.3",
    "@testing-library/react": "^14.0.0",
    "@testing-library/user-event": "^14.5.1",
    "@types/jsdom": "^21.1.3",
    "@types/react": "^18.2.15",
    "@types/react-dom": "^18.2.7",
    "@typescript-eslint/eslint-plugin": "^6.0.0",
    "@typescript-eslint/parser": "^6.0.0",
    "@vitejs/plugin-react-swc": "^3.3.2",
    "@vitest/coverage-istanbul": "^0.34.6",
    "eslint": "^8.45.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "eslint-plugin-react-refresh": "^0.4.3",
    "jsdom": "^22.1.0",
    "typescript": "^5.0.2",
    "vite": "^4.4.5",
    "vitest": "^0.34.6"
  }
}
```
