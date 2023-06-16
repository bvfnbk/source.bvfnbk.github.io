---
title: "Components as Bundle"
date: 2023-03-20T11:32:53+01:00
includeInPostList: true
draft: false
tags:

- web
- html
- typescript
- events
- test
- webpack
- jest

---

The latest [example](/posts/example-web-components/message-input/) defined another component which is transpiled,
together with the [first](/posts/example-web-components/ts-hello-message/) component into a `dist/` directory containing
an `index.html` ready to use the components.

There is nothing especially wrong with the proposed structure - one _can_ build components using _TypeScript_ starting
from this foundation. However, the build layout contains quite a few errors and pitfalls making it more difficult than
actually required:

- `import` of other modules not possible without further ado,
- components and other modules need to be included in `index.html` to be usable,
- paths to include scripts must match the location the transpiled files are put into

and there are definitely more which I have not found yet.

The sources to this web component and others can be found on GitHub: https://github.com/bvfnbk/example-web-components

## An Excuse ...

Originally, this whole project was intended to develop an environment to work with web components using _small_ (if not
_minimal_) increments:

- adding one small feature after the other without loosing any functionality and
- each step easy to understand

until I reach a sufficiently convenient setup or end up using _React_, deleting the posts/repos again.

Unfortunately, I stumbled across a problem for which I failed to spot the immediate (minimal) fix. Too early, I
considered the creation of a bundle to be the only practical solution to the problem at hand. 

Now, I do not believe this anymore.

## Comments on the Sources

All `.ts` files have initially been put into the same `src/ts/` directory. This works for a proof of concept but quickly
gets messy when other _TypeScript_ modules are being added (especially when following a "one class/interface/enumeration
per file" rule).

### Step 1: Add More Structure

Currently, I have only web components, thus, all `.ts` files are moved to `src/ts/components/`. Changing the `src`
paths in the corresponding `<script>` tags in the `src/public/index.html` file should be sufficient. However, the
_TypeScript_ configuration causes the compiler to put all results directly into `dist/js/` source - flat, without
matching the source directory structure.

This can be easily fixed: update `tsconfig.json` and add the property `rootDir` pointing to `src/ts` to the
`compilerOptions`:

```json
{
  "compilerOptions": {
    "rootDir": "src/ts"
  }
}
```

Rebuild `dist/` and open `dist/index.html`.

### Step 2: Create Entry Point

The initialization code is currently distributed across several files:

- the `src/public/index.html` &hellip;
  - &hellip; includes the components using `<script src="...">` tags and
  - &hellip; registers the required event listeners which actually perform the update.
- the individual components register themselves using `customElements.define(...)`.

OK, not so many files, but this may change in the future. This change suggests the creation of a `src/ts/index.ts` file
which, in the end, should look like the following snippet:

```typescript
import HelloMessage from './components/HelloMessage';
import MessageInput from './components/MessageInput';

customElements.define('hello-message', HelloMessage);
customElements.define('message-input', MessageInput);

const greeter = document.getElementById('greeter');
document.getElementById('msgInput')!.addEventListener(
  'MessageUpdateEvent',
  (event: Event) => {
    const custom = event as CustomEvent;
    greeter!.setAttribute('name', custom.detail);
  });
```

#### A Short Overview of ES6 Modules

Recent web browser allow the usage of ES6 modules. This allows the definition of a module, e.g. the component defined in
`dist/js/components/HelloMessage.js` as an _ES6 Module_, i.e.

```typescript
export default class HelloMessage extends HTMLElement {}
```

The module defines a _default export_ which can be used in _other_ ES6 modules, e.g. the entry point `dist/js/index.js`
like

```typescript
import HelloMessage from './components/HelloMessage.js';
```

The module `js/index.js` can be included using a `<script>` tag

```html
<script src="js/index.js" type="module"></script>
```

**Please note:**

- The attribute `type="module"` is required for the web browser to handle the _JavaScript_ file as module.
- The `import` instructions in `js/index.js` cause the browser to load the named modules from the given path.
- The page must be served from an HTTP service (web browsers will not load ES6 modules from `file://` URLs).
- The browser composes an HTTP request given the _relative_ path to the module to be imported.
- The `.js` extension is required; the browser gets an `404` otherwise.

#### Quick Fix `import` Instructions

Update `src/ts/index.ts` and append the `.js` extension to the module `import` instructions, i.e.

```typescript
import HelloMessage from './components/HelloMessage.js';
import MessageInput from './components/MessageInput.js';
```

Build `dist/` and open `dist/index.html` and the page should work again. The _Karma_ tests are not running though:
_Karma_ fails to be able to import the components-under-test as these are ES6 modules which `export` themselves and 
_Karma_ seems to not understand (at least without further ado).

#### Implemented "Fix"

##### Creating a Bundle

**Please note:** There are several tools out there for bundle creation, e.g.

- _Webpack_
- _Parcel_
- `esbuild`
- _Vite_.

to name but a few from which I chose _Webpack_ (seems to be the top dog; eventually about to get replaced with _Vite_ or 
whatever). Install it with

```shell
npm install --save-dev webpack webpack-cli ts-loader
```

and create the following `webpack.config.js` in the working directory:

```javascript
const path = require('path');
module.exports = {
  mode: 'development',
  entry: './src/ts/index.ts',
  devtool: 'inline-source-map',
  module: {
    rules: [
      {
        test: /\.ts$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  resolve : {
    extensions: [
      '.ts',
      '.js'
    ]
  },
  output: {
    filename: 'bundle.js',
    path : path.resolve(__dirname, 'dist', 'js')
  }
}
```

**Please note:**

- This defines the entry point `src/ts/index.ts` from which _Webpack_ builds the tree of the modules to be included in
  the bundle.
- It adds a rule to pipe all `.ts` files through the `ts-loader`.
- It defines the desired output file, namely `dist/js/bundle.js`.


Now, fix `src/public/index.html` to load the `dist/js/bundle.js` file and _not_ the entry point directly. It should look
like

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport"
        content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Example / Web Components / Greeter</title>
</head>
<body>
<message-input id="msgInput"></message-input>
<br/>
<hello-message id="greeter" name="Friend"></hello-message>
<script src="js/bundle.js" type="module"></script>
</body>
</html>
```

The script section can be updated in the `package.json`:

```json
{
  "scripts" : {
    "webpack" : "webpack",
    "build" : "npm-run-all prepare webpack copy-assets"
  }
}
```

The `compile` script (using `tsc`) is not required anymore for building: _Webpack_ handles all _TypeScript_ files
through its `ts-loader`. Thus, it can be _replaced_ with a _webpack_ script.

Now try building it and open `dist/index.html`.

##### Fixing the Tests

_Webpack_ does not fix the tests though: _Karma_ still cannot handle the `export` instructions. I tried out different 
configurations, different packages and plugins to handle _TypeScript_, different preprocessors or transforms to
handle the tests for _Karma_ and, unfortunately, to no avail.

**Please note:** Most certainly, the problem sits in front of the keyboard: I am sure, that I am not the only one trying
to test _TypeScript_ front-end code with _Karma_ and _Jasmine_.

But never fear, I was not especially happy with the way _Karma_ and _Jasmine_ worked anyway. _Karma_ sometimes failed to
start the browsers and the reports were not as I expected (I tried different output formats).

This is the reason I did not want to put more efforts into getting _Karma_ to work and why I actually switched to
_Jest_.

First remove all _Karma_ and _Jasmine_ related packages and install

- `@types/jest`
- `jest`
- `jest-environment-jsdom` and
- `ts-jest`

Create a _Jest_ configuration file `jest.config.js` with the following contents 

```javascript
/** @type {import('ts-jest').JestConfigWithTsJest} */
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
};
```

and remove the `karma` script from the `package.json`. The `test` script may then simply call `jest`.

On the one hand, _Jest_ uses the same test specification language (`describe`, `it` etc.) but on the other hand, there
are at least _some_ differences:

- Assertions:
  - `toBeTrue()` is actually `toBeTruthy()`
  - `toBeFalse()` is actually `toBeFalsy()`
- Mocking: No `spyOn` in _Jest_ but _explicit_ patching of the object, e.g.

  ```typescript
  const event = new Event('whatever');
  event.stopPropagation = jest.fn(() => {});
  ```
  
There is still an issue that some tests fail: _Jest_ uses a specific _environment_ for front-end tests. This environment
is required to provide `document` and `customElements` to the tests and is rather picky when it comes to the asserted
property, e.g. a test checking `innerText` of a component which updates its `innerHTML` property will fail.

_Karma_ uses the browsers to run the tests and those environments are more forgiving.

**Please note:** The tests are not watched anymore and must be _explicitly_ run - but they are running faster (on the
good side).

## Summary

Bundling the _TypeScript_ modules has been added to the
[<message-input> Web Component](/posts/example-web-components/message-input/) and tests have been made to work (again) -
albeit by switching the test framework. Some minor improvements have been implemented to prepare the grounds for future
features (e.g. hot module reloading etc.).

## Resources

- https://caniuse.com/es6-module
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules
- https://webpack.js.org/concepts/
