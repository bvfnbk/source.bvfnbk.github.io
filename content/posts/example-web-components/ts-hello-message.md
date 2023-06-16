---
title: "A <hello-message> TypeScript Web Component"
date: 2023-03-03T10:31:44+01:00
includeInPostList: true
draft: false
tags:

- web
- html
- typescript
- web-component
- test
- karma
- jasmine
---

[A `<hello-message>` Web Component](/posts/example-web-components/hello-message/) introduced vanilla web components
using _JavaScript_. This is a good start but, out of reasons, using _JavaScript_ might not be desirable. 

At the moment, there is only one component and its test and just right now might be a good moment to convert the sources
to _TypeScript._

## Preparation

Create a new package and simply copy the `src/public/js/HelloMessage.js` to `src/ts/HelloMessage.ts`.

**Please note:** The component is basically left "as is" at the moment for the `index.html` to work without changes.

Now copy the `src/public/index.html` to `src/public/index.html` but we are not done yet. Install the required
dependencies for _TypeScript_, i.e.

```shell
npm install --save-dev typescript
```

and add the `tsconfig.json` configuration file

```json
{
  "compilerOptions": {
    "module": "ES6",
    "target": "ES6",
    "alwaysStrict": true,
    "strictNullChecks": true,
    "noImplicitAny": true,
    "noImplicitReturns": true,
    "noImplicitThis": true,
    "removeComments": true
  },
  "include": [
    "src/ts/**/*.ts"
  ],
  "exclude": [
    "node_modules"
  ]
}
```

**Please note:** This probably causes the IDE to show some errors in the copied sources. These can be fixed
by

- adding types to the method signature of `attributeChangedCallback`
- add non-`null` assertion operator to accesses to the nullable fields, e.g.
  - `this.shadowRoot` and
  - `this._wrapper`
- add an empty `export {}` statement to prevent the error message regarding the redeclaration of the `template`
  block-scoped variable.

At the moment it is not possible to open the `index.html` file in the browser. The _TypeScript_ source is not
transpiled yet and there is no _JavaScript_ version to be found at the specified location. There is another step
required.

## Building

This step adds the required "Build" step to the module. To be more precise, this step will add scripts to create a
directory containing all required files (a _distribution_) and to remove previously created _distributions_.

The `build` step is supposed to perform the following steps:

1. Prepare a directory for the distribution.
2. Copy static assets (i.e. the `src/public/index.html` file)
3. Compile _TypeScript_ sources and put them into `dist/js`.

The last step can be configured in the `tsconfig.json` file: add the `outDir` property to the `compilerOptions` object
pointing to `dist/js`. With `shx` and `npm-run-all`, this results in a `scripts` section (in the `package.json`) like:

```json
{
  "scripts" : {
    "clean": "shx rm -rf dist",
    "prepare": "shx mkdir -p dist",
    "compile": "tsc",
    "copy-assets": "shx cp src/public/index.html dist/",
    "build" : "npm-run-all prepare compile copy-assets"
  }
}
```

Run `npm run build` to create the distribution in `dist/`. Open `dist/index.html` in your browser.

## Testing

Copy

- source test specification `test/HelloMessage.spec.js` to `src/test/HelloMessage.spec.ts` and
- the _Karma_ configuration `karma.conf.js` 

and ignore the errors shown by the IDE at the moment.

Install the same dependencies as described in 
[Testing the `<hello-message>` Web Component](/posts/example-web-components/testing-hello-message/) and add the types
for `jasmine` and `karma-typescript`, e.g.

```shell
npm install --save-dev karma \
  jasmine-core \
  karma-jasmine \
  karma-chrome-launcher \
  karma-firefox-launcher \
  karma-coverage \
  karma-typescript \
  @types/jasmine
```

Add the `karma` script to the `package.json`, e.g.

```json
{
  "scripts" : {
    "karma" : "karma start"
  }
}
```

The `karma.conf.js` needs to be updated, e.g.

```js
module.exports = function (config) {
  config.set({
    frameworks: ['jasmine', 'karma-typescript'],
    files: [
      { pattern: 'src/ts/**/*.ts' },
      { pattern: 'src/test/**/*.ts' }
    ],
    preprocessors: {
      'src/ts/**/*.ts' : 'karma-typescript',
      'src/test/**/*.ts' : 'karma-typescript'
    },
    karmaTypescriptConfig: {
      compilerOptions: {
        module: 'ES6',
        target: 'ES6'
      },
      exclude: [
        'node_modules'
      ]
    },
    reporters: ['progress', 'karma-typescript'],
  });
};
```

**Please note:**

- The `@types/jasmine` package should fix the missing symbols in the test specification.
- The `karma-typescript` preprocessor compiles tests and components to _JavaScript_ before running the test.
- The `karmaTypescriptConfig` configures the output target.j

## Test Simplification

**Please note:** The test from 
[Testing the `<hello-message>` Web Component](/posts/example-web-components/testing-hello-message/)
is supposed to run without any modification.

The changes sketched in the following source code snippet improve the code style of the original specification and add
some type information to the fields.

```typescript
describe('The <hello-message> element', () => {
    let defaultElement:HTMLElement;
    let defaultShadow: ShadowRoot;

    let customElement: HTMLElement;
    let customShadow: ShadowRoot;

    beforeEach(() => {
        defaultElement = document.createElement('hello-message');
        defaultShadow = defaultElement.shadowRoot as ShadowRoot;

        customElement = document.createElement('hello-message');
        customElement.setAttribute('name', 'Friend');
        customShadow = customElement.shadowRoot as ShadowRoot;

        document.body.append(defaultElement, customElement);
    });

    afterEach(() => {
        // Cleanup DOM
        document.body.removeChild(defaultElement);
        document.body.removeChild(customElement);
    });

    it('should change the message back to the default when the "name" attribute is removed.', function () {
        expect(customShadow.getElementById('wrapper').innerText).toBe('Hello Friend!');
        customElement.removeAttribute('name');
        expect(customShadow.getElementById('wrapper').innerText).toBe('Hello World!');
    });
});
```

## Sources

As usual, the sources to the web component can be found on GitHub: https://github.com/bvfnbk/example-web-components
