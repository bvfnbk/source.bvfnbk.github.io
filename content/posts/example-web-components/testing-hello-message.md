---
title: "Testing the <hello-message> Web Component"
date: 2022-10-05T12:34:34+02:00
includeInPostList: true
draft: false
tags:

- web
- html
- javascript
- web-component
- test
- karma
- jasmine
---

The created [web component](/posts/example-web-components/hello-message/) (admittedly) is very simple and does not 
implement sophisticated behaviour (or _any_ behaviour for that matter). It should be tested nonetheless.

The part of the software stack actually running the tests is called a _test runner_. Another important part is the _DSL_
to be used to write the tests. Basically install the following packages:

- `karma` (test runner)
- `jasmine-core` (test DSL)
- `karma-jasmine`
- `karma-chrome-launcher`
- `karma-firefox-launcher`

**Please note:**

- _Jasmine_ provides the DSL to write the tests (for _Behaviour Driven Development (BDD)_).
- The different launchers allow testing for different web browsers. 

Run

```shell
./node_modules/karma/bin/karma start
```

to test _Karma_.

> **Improvement:**  Create a `npm` script in the `package.json` file, e.g.
> 
> ```json
> {
>   "scripts": {
>     "karma" : "karma start"
>   }
> }
> ```
> to start _Karma_.

Create the file `karma.config.js` in the working directory with the following contents:

```js
module.exports = function (config) {
  config.set({
    basePath: '',
    frameworks: ['jasmine'],
    files: [
      {pattern: 'src/public/js/HelloMessage.js', watched: true},
      {pattern: 'src/test/**/*.spec.js', watched: true}
    ],
    exclude: [],
    preprocessors: {},
    reporters: ['progress'],
    port: 9876,
    colors: true,
    logLevel: config.LOG_INFO,
    autoWatch: true,
    browsers: ['Chrome', 'Firefox'],
    singleRun: false,
    concurrency: Infinity
  });
};
```

Create a test (e.g. `HelloMessage.spec.js`) in `src/test/` with the following contents:

```js
describe('The <hello-message> element', () => {
  beforeEach(() => {
    // Add two different elements to the DOM; one w/o the 'name' attribute and the other with a configured 'name':
    document.body.insertAdjacentHTML('afterbegin', `<hello-message id="default"></hello-message>`);
    document.body.insertAdjacentHTML('afterbegin', `<hello-message id="custom" name="Friend"></hello-message>`);
  });

  afterEach(() => {
    // Remove the elements again:
    document.body.removeChild(document.getElementById('default'));
    document.body.removeChild(document.getElementById('custom'));
  })

  it('should contain the default message if the input element has no value set.', function () {
    const element = document.getElementById('default');

    expect(element.shadowRoot.getElementById('wrapper').innerText).toBe('Hello World!');
  });

  it('should contain the message containing the value provided using the "name" attribute.', function () {
    const element = document.getElementById('custom');

    expect(element.shadowRoot.getElementById('wrapper').innerText).toBe('Hello Friend!');
  });

  it('should change the message when the "name" attribute is added.', function () {
    const element = document.getElementById('custom');

    expect(element.shadowRoot.getElementById('wrapper').innerText).toBe('Hello Friend!');
    element.setAttribute('name', 'Jasmine');
    expect(element.shadowRoot.getElementById('wrapper').innerText).toBe('Hello Jasmine!');
  });

  it('should change the message when the "name" attribute is updated.', function () {
    const element = document.getElementById('custom');

    expect(element.shadowRoot.getElementById('wrapper').innerText).toBe('Hello Friend!');
    element.setAttribute('name', 'Jasmine');
    expect(element.shadowRoot.getElementById('wrapper').innerText).toBe('Hello Jasmine!');
  });

  it('should change the message back to the default when the "name" attribute is removed.', function () {
    const element = document.getElementById('custom');

    expect(element.shadowRoot.getElementById('wrapper').innerText).toBe('Hello Friend!');
    element.removeAttribute('name');
    expect(element.shadowRoot.getElementById('wrapper').innerText).toBe('Hello World!');
  });
});
```

**Please note:** Apparently, it is possible to&hellip;

- &hellip; access the DOM using `document`,
- &hellip; _modify_ the DOM,
- &hellip; retrieve elements from the DOM and
- &hellip; access the shadow root of custom elements.

> _Is Jasmine the reason, why the test is working?_
> 
> The test has access to the DOM and provides the standard `describe()`/`it()` functions. Apparently



## Sources

The sources to the web component can be found on GitHub: https://github.com/bvfnbk/example-web-components
