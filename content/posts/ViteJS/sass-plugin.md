---
title: SASS plugin for ReactJS
tags: ['vitejs', 'SASS', 'ReactJS']
publishDate: 2024-10-15
---

[vite-plugin-sass-dts](https://www.npmjs.com/package/vite-plugin-sass-dts)

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
