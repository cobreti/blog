---
title: "Declare Custom Tags in ReactJS"
description: "How to declare custom tags in ReactJS"
published: 2024-10-12
tags: ["ReactJS", "TypeScript"]
---
Here’s the way to declare custom tags not being part of the React application so they don’t appear as an error.
```typescript
declare global {
    namespace JSX {
        interface IntrinsicElements {
            "test-address-form": React.DetailedHTMLProps<React.HTMLAttributes<HTMLElement>, HTMLElement>
        }
    }
}
```
