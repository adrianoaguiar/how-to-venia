# Project Structure & Routing

## Overview

In the previous section, you prepared your local development environment by installing the necessary software 
and creating your first PWA Studio site using the `@magento/pwa` scaffolding command. 

Now, lets take a closer look into the code generated by that command.

## Project Structure

The  `@magento/pwa` scaffolding command generates all the files and directories to set you project up quickly. 

As with most React Apps the components for the site are located in the **./src/** directory.
But you will notice there are not many files in this directory.

This is because PWA Studio uses a modular approach to loading React components with webpack from the various PWA Studio packages,
such as:

-   [@magento/peregrine][]
-   [@magento/venia-ui][]

These packages are used to create a PWA Studio app based on the [Venia Storefront (Concept)][].
You can find the source code for them in your project in the **node_modules/@magento** directory.

```tree
├── node_modules
│   └── @magento
│       ├── peregrine
│       ├── pwa-buildpack
│       ├── upward-js
│       └── venia-ui
│           └── lib
│               ├── RootComponents
│               └── components
│                   └── App
└── src
    ├── ServiceWorker
    ├── drivers.js
    ├── index.css
    ├── index.js
    ├── registerSW.js
    └── store.js
```

In subsequent tutorials we will demonstrate how you can begin customising your project, 
by copying the React components from the _node_modules/@magento/_ to your projects _./src/_ directory.

Your project will follow the same structure as the [Venia Storefront (Concept)][].
It is worth reviewing Magento's docs on [Venia project structure][] and [Modular components][] for this reason.

## Routing

PWA Studio needs to handle both static routes and all of the different types of 
routes from Magento (category, product, CMS, customer, sales etc) as well as all the Magento URL rewrites. 

In this section you will be introduced to how routing works in PWA Studio, before we add a custom static route in the next tutorial.
Don't worry if you don't understand everything here straight away, 
it's just to give you a general idea and is something you can come back to later if needed.

### Server Side Routing

The Venia storefront use Upward server middleware to proxy requests from the user's client. 
It allows you to configure how requests are proxied in your projects _./upward.yml_ file.

Upward can pre-render the application shell with mustache templates. 
To get a better idea of how PWA Studio's Upward server works, it's recommend to complete the [Hello UPWARD Tutorial][].

### React Routing

Once the user loads the first web page all other requests are handled by the React App like any other Single Page App. 
It re-renders its content in response to navigation actions (e.g. clicking a link) without making 
a request to the server to fetch new HTML and without refreshing the entire page.

In the following sections we'll take a closer look at how React routing is used in your PWA Studio app.    
Install the [react-developer-tools][] extension for chrome as we will use it to inspect the React app in the browser.

First take a look at the **entry point** of the application in _.src/index.js_.
You will find that it is rendering the `<App />` component from `@magento/venia-ui/lib/components/App`.

The `<App />` component is being wrapped by the `<ApolloContext.Provide />` higher-order component (HOC).
This is where the ApolloProvider, the ReduxStore, and the Router configuration are being set. 

![react-components][]

The `<App />` component is using the _node_modules/@magento/venia-ui/lib/components/Routes/routes.js_
file.  This file is using react-router for rendering static routes such as _/search.html_ and _/create-account_.    

The `<MagentoRoute>` component from PWA Studio's Peregrine library **to render Magento's Category & Product routes**.

If we look a little closer we can see that the `<MagentoRoute>` in turn uses the [MagentoRouteHandler][] component 
to resolve the route by querying Magento 2 API, 
it then receives the page type in the response. e.g. *CMS_PAGE*, _CATEGORY_ and _PRODUCT_.

If the URL doesn’t exist, Magento 2 will send out a 404 error.
If the URL exists, MagentoRouter will render a RootComponent which is assigned to the received page type.

All root components must be placed in a *rootComponents* directory with a index.js entry point with 
a commented section to define the pageType for a specific page type. See the [category page root component][] as an example.

## Learn More

-   [Venia Storefront (Concept)][]
-   [Venia project structure][]
-   [Modular components][]
-   [Hello UPWARD Tutorial][]
-   [REACT Higher-Order Components][]
-   [REACT Router][]
-   [Routing with PWA Studio][]

---
- [> see other topics](../../README.md#topics)

[Routing with PWA Studio]: https://magento.github.io/pwa-studio/peregrine/routing/
[Venia Storefront (Concept)]: https://magento.github.io/pwa-studio/venia-pwa-concept/
[Venia project structure]: https://magento.github.io/pwa-studio/venia-pwa-concept/project-structure/
[Modular components]: https://magento.github.io/pwa-studio/venia-pwa-concept/features/modular-components/
[Hello UPWARD Tutorial]: https://magento.github.io/pwa-studio/tutorials/hello-upward/simple-server/
[@magento/peregrine]: https://www.npmjs.com/package/@magento/peregrine
[@magento/venia-ui]: https://www.npmjs.com/package/@magento/venia-ui
[react-developer-tools]: https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi
[react-components]: ./images/react-components.png
[REACT Higher-Order Components]: https://reactjs.org/docs/higher-order-components.html
[REACT Router]: https://reacttraining.com/react-router/
[MagentoRouteHandler]: https://github.com/magento/pwa-studio/blob/develop/packages/peregrine/lib/Router/magentoRouteHandler.js
[category page root component]: https://github.com/magento/pwa-studio/blob/develop/packages/venia-ui/lib/RootComponents/Category/index.js
