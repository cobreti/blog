---
title: Vanilla web component
publishDate: 2024-10-21
tags: ['web component', 'vanilla']
---

### Description

Vanilla web component containing a form and made using typescript, html and scss: no framework. The form contains three fields : Address, City and Postal Code. A state is used within the component to old values whenever they’re changed (a little like redux) and a notification is sent when this state content changes

[GitHub Link](https://github.com/cobreti-demos/web-components/tree/master/Components/Vanilla)

### structure

Web components needs to be tested, both by unit tests and visually. One way to do this would be to have project be both web-component and application at the same time with multiple config depending you want to build what. Possible but a headache to maintain: you need multiple tsconfig.json and vite.config.ts files.

An alternative to this is to simply have two projects: one being the web-components projects and a second being the test application. That’s the structure I chose and it looks like the following :

```
- Vanilla
     - TestApp
     - Web-Components
```
### Component Visual

The visual for this component is pretty simple: 3 simple fields with labels.

The dashed background comes from the parent and not the component itself.

![Component Visual](web-component/vanilla/component-visual.png)


### HTML and CSS insertion

Insertion of HTML and CSS has to be done manually. They are first imported as string within the typescript file and then added to the shadow DOM.

To properly import an HTML template, we need to add « ?raw » at the end of the file path. This prevent the import from attempting to parse the html as javascript which would result in an invalid content error. The « ?raw » attribute is part of vitejs [static asset handling](https://vitejs.dev/guide/assets.html).

Here’s an example of the code doing the import and html/css insertion.

```Typescript
import template from './address-form.html?raw';
import styles from './address-form.scss?inline';

...

    _shadowRoot: ShadowRoot | null;

...

        const templateNode = document.createElement('template');
        templateNode.innerHTML = template;

        const styleNode = document.createElement('style');
        styleNode.textContent = styles;
        

        this._shadowRoot = this.attachShadow({mode: 'open'});
        this._shadowRoot.appendChild(styleNode);
        this._shadowRoot.appendChild(templateNode.content.cloneNode(true));

```

### events and state

This component listen on each control input to dispatch an internal event responsible of updating the component state. When a component state changes, another event (again with a debounce) is being dispatched with the old and new state content.

To prevent having tons of events when the user is updating the field, a debounce time is being used through the RxJs library. Those listeners are being set on the connectedCallback of the web component and being removed when the component is disconnected from the DOM through the use of [RxJs](https://rxjs.dev/) takeUntil on another event: disconnected.

Here’s a snippet of code setting up an event listener for an input.


```Typescript
    ...
    
    
            const postalCodeElm = this._shadowRoot.getElementById('postal-code') as HTMLInputElement;
            if (postalCodeElm) {
                fromEvent(postalCodeElm, 'input')
                    .pipe(
                        debounceTime(this._debouceTime), 
                        takeUntil(this._disconnected$))
                    .subscribe(ev => this.onPostalCodeChanged(postalCodeElm, ev));
            }
            
    ...

    onPostalCodeChanged(elm: HTMLInputElement, ev: Event) {
        this.setState({
            postalCode: elm.value
        });
    }

```

Below a diagram describing the events propagation toward the outside of the component.

![events](web-component/vanilla/events.png)


### web-component events

It would be nice to have the possibility to use attribute like on-state-changed directly in the html source, but this would imply using the eval function to parse the javascript, which could lead to security issues.

The safest way to manage events is to use html event system.

Here’s the code to dispatch the event outside the web-component. It’s important to set bubbles and composed to true so the event can travel outside the shadow-root.

```Typescript
this.dispatchEvent(new CustomEvent('state-changed', {
                    bubbles: true,
                    composed: true,
                    detail: {
                        oldState: data.oldState,
                        changes: data.changes,
                        state: data.state
                    }
                }))
```

Here’s the listener part from the code using the web-component:

```Typescript
const testAddressForm = document.getElementById('test-address-form');
         testAddressForm?.addEventListener('state-changed', (ev) => {
            console.log(ev);
         });
```


### Unit Testing

Unit testing web component can be challenging. The structure needs to be started well or else it gets impossible to test. My first attempt was to create everything directly in the class derived from HTMLElement ; what a mistake. It ends up that HTMLElement is having problem when used with vitest and jsdom. The solution was to separate things in different objects.

My web component was wrapping a simple address form so I divided things the following way :

AddressForm class
This class contains the main functionality of the component and we can perform unit tests on it easily.
AddressFormComponent
This is the web component wrapper that uses the AddressForm class to make it a web component.
Doing things this way allow us, not only to test the AddressForm component easily, but to use it without it being a web component if needed.

![unit tests](web-component/vanilla/unit-tests.png)


### Usage in React
When using a custom web component within react, the IDE will often give an error where the tag is added because it doesn’t recognize the element.

To make this element known we need to declare it in JSX.IntrinsicElements interface as follow :

```Typescript
declare global {
    namespace JSX {
        interface IntrinsicElements {
            "test-address-form": React.DetailedHTMLProps<React.HTMLAttributes<HTMLElement>, HTMLElement>
        }
    }
}
```

We can then use it within our component without any error.

```HTML
function App() {

    return (
        <div className="container">
            <div className="header"></div>
            <div className="content">
                <div className="form">
                    <test-address-form></test-address-form>
                </div>
            </div>
            <div className="footer"></div>
        </div>
  )
}
```
