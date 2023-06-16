---
title: "A <message-input> Web Component"
date: 2023-03-08T09:59:18+01:00
includeInPostList: true
draft: false
tags:

- web
- html
- typescript
- events
- test
- karma
- jasmine

---

[The example](/posts/example-web-components/ts-hello-message/) still uses plain JavaScript to listen to `input` events
and to update the message. This post replaces the original `<label>` and `<input>` element with a new component.

The sources to this web component and others can be found on GitHub: https://github.com/bvfnbk/example-web-components

The component gets defined in `src/ts/MessageInput.ts` and its template basically replaces the original elements

```html
<label for="someInput">Message:</label>
<input id="someInput" type="text"/>
```

and implicitly handles the `input` event on the `<input>` element. The most important changes are:

```typescript
const messageInputTemplate: HTMLTemplateElement = document.createElement('template');

messageInputTemplate.innerHTML = `
<div>
    <label for="someInput">Message:</label>
    <input id="someInput" type="text"/>
</div>
`;

class MessageInput extends HTMLElement {
  private shadow: ShadowRoot;
  private inputElement: HTMLInputElement;
  private readonly eventHandler: EventListener;

  constructor() {
    super();

    this.shadow = this.attachShadow({mode: 'open'});
    this.shadow.appendChild(messageInputTemplate.content.cloneNode(true));
    this.inputElement = this.shadow.getElementById('someInput')! as HTMLInputElement;
    this.eventHandler = (event: Event) => this.inputEventHandler(event as InputEvent);
  }

  connectedCallback() {
    this.inputElement.addEventListener('input', this.eventHandler);
  }

  disconnectedCallback() {
    this.inputElement.removeEventListener('input', this.eventHandler);
  }

  private inputEventHandler(inputEvent: InputEvent) {
    inputEvent.stopPropagation();

    this.dispatchEvent(new CustomEvent<string>(
      'MessageUpdateEvent',
      {detail: this.inputElement.value}
    ));
  }
}
```

The characteristics of this component are quickly summarized:

- The concrete event handler (`eventHandler: EventListener`) is added in the `connectedCallback` lifecycle method and
  cleaned up in the `disconnectedCallback`.
- The actual input event handler stops the event propagation; otherwise, the `input` event would continue upwards.
- A new, custom event is being dispatched with the input value as `details`. The parent may then add a listener
  for `MessageUpdateEvent` events.
- The test specification in `src/test/MessageInput.spec.ts` asserts the proper event handling.
