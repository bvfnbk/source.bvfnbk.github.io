---
title: "Overview: Web Components"
date: 2022-08-25T10:04:45+02:00
includeInPostList: true
draft: false
tags:

- web
- html
- javascript
- typescript
- web-component

---

## Examples

* [A `<hello-message>` Web Component](/posts/example-web-components/hello-message/)
  * Define a JavaScript web component.
  * Add a watched `string` attribute.
  * Update the attribute using JavaScript.
* [Testing the `<hello-message>` Web Component](/posts/example-web-components/testing-hello-message/)
* [A `<hello-message>` TypeScript Web Component](/posts/example-web-components/ts-hello-message/)
  * Same as the JavaScript web component.
  * &hellip;only using TypeScript.
  * Test specifications from 
    [Testing the `<hello-message>` Web Component](/posts/example-web-components/testing-hello-message/) 
    have been ported over.
* [A `<message-input>` Web Component](/posts/example-web-components/message-input/)
  * Convert the `<label>` and `<input>` element to a dedicated component.
  * Handle `input` event and continue with another, custom event.
  * Test event handling.

## Listening to Attribute Changes

Attributes may be used during the construction of the shadow element DOM using `getAttribute(name)`. This uses the value
of the attribute which is set in its definition.

Only registered attributes trigger the `attributeChangedCallback`. Use the static `observedAttributes` property to
announce the attributes which should be monitored.

## Always Expand Tags

The following snippet looks correct at the first sight:

```html
<hello-message name="Friend"/>
<hello-message name="Another Friend"/>
```

Both elements are _empty_ and properly closed - but what is valid for XML/XHTML is handled differently in HTML5. The
second greeting eventually ends up as a _child_ element of the first `<hello-message />` node disappearing from the
page. 

The standard allows omitting end tags only for _certain_ elements and the parser will sanitize its input, adding the
required end tag at the wrong position in the DOM. In fact, IntelliJ warns about these empty elements.

> **Rule of Thumb:**
> 
> Always expand custom elements.

## Custom Element Names

* Must start with a _lower-case_ ASCII character.
* Must contain a hyphen (`-`)

This should already avoid the accidental usage of an already existing HTML element name.

## Shadow Root

Modes:

* `open`
  * Make the shadow root accessible by _JavaScript_ from the _outside_
* `closed`
  * The shadow root is not supposed to be accessible using _JavaScript_ from the outside.

## Lifecycle Methods

* `connectedCallback()`
  * Should contain long-running/asynchronous code for setup.
* `disconnectedCallback()`
* `adoptedCallback()`
* `attributeChangedCallback(name, oldValue, newValue)`
  * Called before `connectedCallback()` (if the element definition contains monitored attributes).

**Please note:**

- the `attributeChantgedCallback` may be/is called _before_ the `connectedCallback` (take care of fields required for
  the update and initial values).

## Resources

### Mozilla Developer Network

The landing page can be found here: https://developer.mozilla.org/en-US/docs/Web/Web_Components

There are links to tutorials, to the API and more elaborate examples, e.g.

* https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements
* https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM

### W3 Schools

A detailed list of existing tag names can be found here: https://www.w3schools.com/tags/default.asp

Just in case you look for inspiration...

### HTML Standard

### Closing Tags

The section https://html.spec.whatwg.org/multipage/syntax.html#syntax-elements provides more details about those
elements which do not require a closing tag.

#### Custom Element Constructors

https://html.spec.whatwg.org/multipage/custom-elements.html#custom-element-conformance describes in more detail, what
requirements there are for _custom element constructors_.
