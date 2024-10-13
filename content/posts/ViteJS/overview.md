---
title: ViteJS Overview & Configuration
description: What is ViteJS and some configuration hints.
publishDate: 2024-10-13
tags: ['ViteJS', 'TypeScript', 'Configuration', 'SASS', 'Unit tests']
---
# Description

Front-end generation tool like WebPack but optimized to process code on-demande instead of ahead-of-time.
It uses native browsers ESM (ES Modules) to split the code in different bundles and let the browser perform directly the loading of modules.

# Bundle base vs Native ESM based dev server

![alt text](vitejs/bundle-vs-ESM-881x1024.png)

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

# SASS plugin
<div style='text-align: right'><a href="https://www.npmjs.com/package/vite-plugin-sass-dts">vite-plugin-sass-dts</a></div>

This plugin allow us to import SASS and SCSS directly into react tsx/jsx files using the following syntax.

```Typescript
import React from 'react'
import styles from "./card.module.scss";

function Card(props: CardProps): JSX.Element {
    return (
      <div></div>
    )
}
```

# Server

The server section defines the port and host address to listen to. Here we choose port 8000 and host 0.0.0.0.

Why not using 127.0.0.1 which is the default ? Simply to allow us to use hostname defined in the hosts file instead of only localhost.

For example, if the hosts file contains the following line « 127.0.0.1 demo », using the following url « http://demo:8000/ » would fail if server listen on 127.0.0.1 but succeed if listening on 0.0.0.0.

# Variables

ViteJS exposes some [variables](https://vite.dev/guide/env-and-mode.html) to the browser. Here’s some useful ones.

| variable | content |
|:---:|:---:|
| import.meta.env.MODE | mode the project was build : (development, production) |


# Unit tests using Vitest

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

it’s important to note that by default the defineConfig function is obtained from vite using the following include : « import { defineConfig } from ‘vite’ ». This will have the result of generating an error since test is not defined. To fix this error you have to import it from vitest using the following :

« import {defineConfig} from ‘vitest/config’ »

# Cleaning up Mocks

To make sure all mocks are being cleared between tests, it’s important to add the following lines to afterEach :

```TypeScript
    afterEach( () => {
        vi.clearAllMocks();
        vi.resetAllMocks();
        vi.resetModules();
        vi.restoreAllMocks();
    });
```
*I had the unpleasant surprise to have the mockImplemention from spyOn not being cleared when doing a simple clearAllMocks. Adding all those cleared the issue.*


# mocking event listeners with JSDOM

Here’s an example of code that required testing. It involved checking that the code was executing when the event is dispatched.

```TypeScript
  const testAddressForm = rootElm.querySelectorAll('.form');

  testAddressForm.forEach( (formElm) => {
      formElm.addEventListener('state-changed', (ev) => {
          console.log(ev);
      });
  })
```

To be able to test this code, we need to dispatch an event to the given element and spy on console.log to know if it’s been called.

Here’s the first draft of the code I attempted to write:

```TypeScript
test('console log called when event triggered', async() => {
        const { app, initSubject } = createDummyApplication();

        mockedDOM = new JSDOM(`<div id='root'>
                                    <div class='form'></div>
                                </div>`);
        global.document = mockedDOM.window.document;

        const formElm = global.document.querySelector('.form');

        expect(formElm).not.toBeNull();
        if (formElm) {
            const main = new MainComponent(app);

            initSubject.next(app);

            const spyConsoleLog = vi.spyOn(console, 'log');
            const ev = new CustomEvent('state-changed', {
                bubbles: false
            });

            formElm.dispatchEvent(ev);

            expect(spyConsoleLog).toBeCalled();
        }
    });

```
Although the code looks right, we always end up with the following error when executing the test :

<span style="color: orange">« TypeError: Failed to execute ‘dispatchEvent’ on ‘EventTarget’: parameter 1 is not of type ‘Event’ »</span>

Took quite a while to find the problem, some even points to an error in JSDom library. Where in fact, the error lies in how the custom event is being created.

Here’s the final and working code


```TypeScript
test('console log called when event triggered', async() => {
        const { app, initSubject } = createDummyApplication();

        mockedDOM = new JSDOM(`<div id='root'>
                                    <div class='form'></div>
                                </div>`);
        global.document = mockedDOM.window.document;

        const formElm = global.document.querySelector('.form');

        expect(formElm).not.toBeNull();
        if (formElm) {
            const main = new MainComponent(app);

            initSubject.next(app);

            const spyConsoleLog = vi.spyOn(console, 'log');
            const ev = new mockedDOM.window.CustomEvent('state-changed', {
                bubbles: false
            });

            formElm.dispatchEvent(ev);

            expect(spyConsoleLog).toBeCalled();
        }
    });
```
The difference lies in which CustomEvent we instantiate : « mockedDOM.window.CustomEvent » vs « CustomEvent ».

Simple difference, but one that can make someone waste much precious time.



