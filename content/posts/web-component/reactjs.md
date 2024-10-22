---
title: ReactJS Web Component
description: create a web component from a ReactJS component
tags: ['reactjs', 'web component']
publishDate: 2024-10-22
---

Creating a web component from a react component could be done manually by implementing the HTMLElement object ourself. The method I chose is to use the [@r2wc/react-to-web-component](https://www.npmjs.com/package/@r2wc/react-to-web-component) ( or [r2wc](https://www.npmjs.com/package/r2wc) as it seems to be moved/forked ) library to do the job.

With this library we simply have to make a call to create the web component and another to add it to the custom elements list.

```Typescript
import r2wc from "@r2wc/react-to-web-component";

const WCHeader = r2wc(Header, {
    shadow: 'open',
    props: {}
});

customElements.define('test-header', WCHeader);
```

### CSS Styling

Styling react component is usually something pretty easy. we simply have to import the css at the beginning of the tsx and use the classes within the component.

```Typescript
import './header.scss';

export function Header () {
    return (
        <>
            <div className="header">
                header test
            </div>
        </>
    )
}
```

When building a web component a problem arise through a library we will either have to import the generated css file from the library or use a plugin like « vite-plugin-css-injected-by-js » to inject our css directly into our javascript.

The result from both of these method is that the css will be added by default to the main DOM while the component will be added to a shadow dom. This will prevent the react component from accessing the style we defined.

The solution to this problem is to import the styles as inline, override the « connectedCallback » of the HTMLElement created by r2wc so we can manually add our style to the shadow-dom.

Here’s an example of the resulting code:

```Typescript
import styles from './header.scss?inline';
import r2wc from "@r2wc/react-to-web-component";

export function Header () {
    return (
        <>
            <div className="header">
                header test
            </div>
        </>
    )
}

const WCHeader = r2wc(Header, {
    shadow: 'open',
    props: {}
});

const oldConnectedCallback = WCHeader.prototype.connectedCallback;

WCHeader.prototype.connectedCallback = function() {
    oldConnectedCallback.apply(this);

    const styleElm = document.createElement('style');
    styleElm.textContent = styles;
    this.shadowRoot.appendChild(styleElm);
}

customElements.define('test-header', WCHeader);
```

Here’s the result in the DOM

![Css-Styling](web-component/reactjs/CSS-Styling.png)

### @cobreti/r2wc package

I decided to fork the r2wc npm package from damianpumar and add the option to give the styles directly. This reduce the complexity of the previous example to this.
[@cobreti/r2wc](https://www.npmjs.com/package/@cobreti/r2wc)

```Typescript
import styles from './header.scss?inline';
import {r2wc} from "@cobreti/r2wc";

export function Header () {
    return (
        <>
            <div className="header">
                header test
            </div>
        </>
    )
}

const WCHeader = r2wc(Header, {
    shadow: 'open',
    props: {},
    styles: styles
});

customElements.define('test-header', WCHeader);
```

### subcomponent styling

When a react component uses other react component, the css of all used component must be imported in the css of the one being used to create the web-component so they can be added under the shadow-root. Otherwise their css won’t appear in the shadow-root.

