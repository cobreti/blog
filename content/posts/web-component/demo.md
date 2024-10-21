---
title: Web Component Demo
Description: Simple demo application using ReactJS as a core and web components for its different parts
publishDate: 2024-10-20
tags: ['web component', 'reactjs', 'demo']
---

### Components

- Login control –> ReactJS
- Address form control –> Vanilla Javascript (no framework)

### Git repository

Projects used to test and build this section are located here: [web-component demo project](https://github.com/cobreti-demos/web-components)

Repository structure:

- Apps
    - appdemo1
- Components
    - React
        - TestApp
        - Web-Component
    - Vanilla
        - TestApp
        - Web-Component
- WebServer

### Modules

![demo architecture](web-component/demo/demoapp1-modules.drawio.png)


### Module loading

Modules are loaded by adding a \<script\> tag with the source pointing to the url containing the web component. To make things easier, a json file is used to store different web-component urls. This file is fetched, parsed and then each modules are being added.

Below are example of a json file as well as the code to load web components.

```Typescript
// web-component.json

[
  {
    "name": "React Web Component",
    "url": "http://localhost:8003/index.es.js"
  },
  {
    "name": "Vanilla Web Component",
    "url": "http://localhost:8002/index.es.js"
  }
]
// web-component-loader.ts

interface WebComponentsEntry {
    name: string;
    url: string;
}

export class WebComponentLoader {

    private _url: string;

    constructor(url: string) {
        this._url = url.replace('{mode}', import.meta.env.MODE);
        console.log(`loading web-components from ${this._url}`);
    }

    async loadWebComponents() {
        try {
            console.log('loading web component');
            const response = await fetch(this._url);

            if (!response.ok) {
                throw new Error(`unable to load web components json : ${this._url}`);
            }

            const text  = await response.text();
            const content = JSON.parse(text) as [WebComponentsEntry];

            content.forEach(entry => {
                this.addWebComponent(entry);
            });
        }
        catch (error) {
            console.error(error);
        }
    }

    addWebComponent(entry: WebComponentsEntry) {
        try {
            const existingElm = document.getElementById(entry.name);

            if (!existingElm) {
                console.log(`loading web-component '${entry.name}' --> ${entry.url}`);
                const scriptElem = document.createElement('script');
                scriptElem.type = 'module';
                scriptElem.id = entry.name;
                scriptElem.src = entry.url;
                document.head.append(scriptElem);
            }
            else {
                console.log(`web-component '${entry.name}' already loaded`);
            }
        }
        catch (error) {
            console.error(error);
        }
    }
}
```

### production vs development

There’s a difference where modules are loaded for production and for development. This requires different json files which include the development mode which is replaced at runtime using the mode variable given by vitejs : « import.meta.env.MODE ».

Here are the files naming :

- web-components.development.json
- web-components.production.json

#### development

In development mode each vite project for web-component is being made available by using « npm run dev » command which makes vite serves the content itself. Thus each web-component source are accessed using different ports as shown in the diagram below.

![demoapp-development](web-component/demo/demoapp1-development.drawio.png)

#### production

For the production mode, a .Net Core module was made to act as a webserver with different routes for each web-component distribution folder. Executing « npm run build » will build a component and makes the build result accessible in the project « dist » folder.

Since all modules are accessed using a single web server the use of subpaths are now possible instead of different ports.

![demoapp-development](web-component/demo/demoapp1-production.drawio.png)