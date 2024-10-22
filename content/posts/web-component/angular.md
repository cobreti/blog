---
title: Angular Web Component
description: creating web component using Angular
tags: ['Angular', 'web component']
publishDate: 2024-10-22
---

### References

[Web Components with Angular](https://islamuad.medium.com/web-components-with-angular-d0205c9db08f)

### Notes

need to use [ngx-build-plus](https://github.com/manfredsteyer/ngx-build-plus) to package the result into a single file

```JSON
"scripts": {
    "ng": "ng",
    "start": "ng serve --single-bundle true",
    "build": "ng build --output-hashing none --single-bundle true --polyfills",
    "watch": "ng build --watch --configuration development",
    "test": "ng test"
  },
```

Need to import polyfills files directly in main.ts for web-component to work using ng serve.

```Typescript
// main.ts

import 'zone.js';
import '@webcomponents/custom-elements/src/native-shim';
import '@webcomponents/custom-elements/custom-elements.min';
```

