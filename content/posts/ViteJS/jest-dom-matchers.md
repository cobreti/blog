---
title: jest-dom - matchers
description: library to add extra asserts for DOM states
publishDate: 2024-10-13
tags: ['ViteJS', 'Unit tests', 'DOM']
---

This library allow us to use some extra asserts for various state of the DOM. Unfortunately types are not well discovered and need a little extra setup.

Here’s an example of what this library allow us to do

```TypeScript
const submitBtn = document.querySelector('input[type=submit]');
expect(submitBtn).not.toBeNull();

//
//  do not use vi.useFakeTimers(); with user.type
//      it makes the test timeout
//
await user.type(usernameInput, usernameValue);
await user.type(pwdInput, passwordValue);

expect(submitBtn).toBeEnabled();
```

### Installation

```
npm install -D @testing-library/jest-dom
```

### tests setup file

We need to create a new file « setupTest.ts » to declare the necessary prototype we will be using.

```TypeScript
import { expect } from 'vitest';
import * as matchers from '@testing-library/jest-dom/matchers';

expect.extend(matchers);

//
//  Those declarations are taken from
//      @testing-library/jest-dom/matchers.d.ts
//  they are needed to extends type for TS syntax
//
declare global {
    namespace jest {
        interface Matchers<R> {
            toBeValid(): R
            toBeInTheDocument(): R
            toBeVisible(): R
            toBeDisabled(): R
            toBeEnabled(): R
        }
    }
}
```

### vite.config.ts setup

```TypeScript
...
  test: {
    environment: 'jsdom',
    coverage: {
      provider: 'istanbul'
    },
    setupFiles: './setupTest.ts',
  },
...
```
