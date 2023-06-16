---
title: "A <hello-message> Web Component"
date: 2022-08-22T16:13:21+02:00
includeInPostList: true
draft: false
tags:

- web
- html
- javascript
- web-component

---

_Vanilla web components_ are custom components written in plain JavaScript - not using any of the usual component
frameworks like _React_, _Vue_ or _Angular_ and alike involved.

The following source code snippet defines a `<hello-message />` component, written in plain _JavaScript_. It
supports a single attribute named `name`:

Create a file `src/public/js/HelloMessage.js` with the following contents:

```javascript
/*
 * Define template:
 * 
 *   - create `template` element
 *   - set contents (`innerHTML`) - including `<style>` block.
 */
const template = document.createElement('template');
template.innerHTML = `
<style>
span#wrapper {
  font-weight: bolder;
  color: brown;
}
</style>
<span id="wrapper">Hello World!</span>
`;

/*
 * Define element
 * 
 *   - ES6 class
 *   - Extend HTMLElement (others are possible)
 */
class HelloMessage extends HTMLElement {
  // Define property names for which change events should be intercepted: 
  static get observedAttributes() {
    return ['name'];
  }

  /*
   * - attach shadow
   * - clone template into shadow root
   * - retrieve elements of interest & define fields
   */
  constructor() {
    super();
    this.attachShadow({mode: 'open'});
    this.shadowRoot.appendChild(template.content.cloneNode(true));
    this._wrapper = this.shadowRoot.getElementById('wrapper');
  }
  
  /*
   * Handle _all_ property change events for which this element is registered for.
   */
  attributeChangedCallback(attributeName, _oldValue, newValue) {
    if (attributeName === 'name') {
      this._wrapper.innerText = `Hello ${newValue ? newValue : 'World'}`;
    }
  }
}

/*
 * Register element.
 * 
 * Note: the element class is not required to be exported.
 */
customElements.define('hello-message', HelloMessage);
```

**Please note:**

- The [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements)
  mentions that all tasks related to the set-up of the component is supposed to take place in the `connectedCallback`
  (cf. [Lifecycle Methods]({{< ref "/posts/example-web-components/overview#lifecycle-methods" >}} "Lifecycle Methods"))
- The suggested implementation follows the
  [specification](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-element-conformance) for custom
  elements which actually is more forgiving.
- Long-running (e.g. _asynchronous_ `fetch` requests) should be handled in the `connectedCallback()` though.

Create the HTML page `src/public/index.html` like this:

```html
<!doctype html>
<html lang="en">
<head>
  <title>Example / Web Components / Greeter</title>
  <script src="js/HelloMessage.js"></script>
</head>
<body>
<label for="someInput">Message:</label>
<input id="someInput" type="text"/> <br/>
<hello-message id="greeter" name="Friend"></hello-message>
<script>
  const greeter = document.getElementById('greeter');
  const inputElement = document.getElementById('someInput');

  inputElement.addEventListener('input', (event) => { greeter.setAttribute('name', event.target.value); });
</script>
</body>
</html>
```

## Sources

The sources to this web component (and more) can be found on GitHub: 

https://github.com/bvfnbk/example-web-components
