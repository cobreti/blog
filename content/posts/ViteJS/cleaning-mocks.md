---
title: cleaning up mocks
tags: ['vitejs', 'vitest', 'mock', 'cleanup']
publishDate: 2024-10-15
---


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
