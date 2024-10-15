---
title: Unit tests configuration
tags: ['vitejs', 'unit tests', 'vitest']
publishDate: 2024-10-15
---
Vite has its own test framework called vitest. Very similar to Jest in syntax and as easy to use.

# Package.json

We simply need to add the following package in our package.json to include unit testing and coverage:

- vitest
- @vitest/coverage-istanbul to use istanbul coverage
- @vitest/coverage-v8 to use v8 coverage

We need to add the following lines under the scripts section to run tests and coverage.

```TypeScript
"scripts": {
    ...
    "test": "vitest",
    "coverage": "vitest run --coverage"
  },
```

# Coverage configuration

Coverage is setup in vite.config.ts file using the following:

```TypeScript
export default defineConfig({
    test: {
        coverage: {
            provider: 'istanbul' // could also use v8
        }
    },
```

## to watch for ...

it’s important to note that by default the defineConfig function is obtained from vite using the following include : « import { defineConfig } from ‘vite’ ». This will have the result of generating an error since test is not defined. To fix this error you have to import it from vitest using the following :

« import {defineConfig} from ‘vitest/config’ »
