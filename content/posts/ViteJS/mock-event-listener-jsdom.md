---
title: Mocking event listener
description: using JSDOM library to mock event listener
tags: ['vitejs', 'vitest', 'mock', 'event', 'jsdom']
publishDate: 2024-10-15
---

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
