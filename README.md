// my-addon/manager.js

import React from 'react';
import { STORY_RENDERED } from '@storybook/core-events';
import { addons, types } from '@storybook/manager-api';
import { createAddon } from '@web/storybook-utils';
import { html, LitElement } from 'lit';

const { createElement } = React;

// Define the web component for the addon
class MyAddonElement extends LitElement {
  constructor() {
    super();
    // Bind the event handlers for consistent reference
    this._handleStoryRendered = this._handleStoryRendered.bind(this);
    this._handleCustomEvent = this._handleCustomEvent.bind(this);
  }

  connectedCallback() {
    super.connectedCallback();
    // Add event listeners when the component is attached to the DOM
    this.addEventListener(STORY_RENDERED, this._handleStoryRendered);
    this.addEventListener('my-addon:custom-event-name', this._handleCustomEvent);
  }

  disconnectedCallback() {
    // Remove event listeners when the component is detached from the DOM
    this.removeEventListener(STORY_RENDERED, this._handleStoryRendered);
    this.removeEventListener('my-addon:custom-event-name', this._handleCustomEvent);
    super.disconnectedCallback();
  }

  _handleStoryRendered(event) {
    // Handle the STORY_RENDERED event
    console.log('Story rendered', event);
  }

  _handleCustomEvent(event) {
    // Handle custom addon events
    console.log('Custom event', event);
  }

  render() {
    return html`
      <div>
        <!-- My Addon Template -->
        <p>My addon content goes here</p>
      </div>
    `;
  }
}

// Define the custom element
customElements.define('my-addon', MyAddonElement);

// Create the React wrapper using the `createAddon` utility
const MyAddonReactComponent = createAddon('my-addon', {
  events: ['my-addon:custom-event-name'],
});

// Register the addon in Storybook
addons.register('my-addon', (api) => {
  addons.add('my-addon/panel', {
    type: types.PANEL,
    title: 'My Addon',
    render: ({ active }) => createElement(MyAddonReactComponent, { api, active }),
  });
});
// my-addon/decorator.js
import { addons } from '@storybook/preview-api';

// Emitting the custom event to the addon from a story
addons.getChannel().emit('my-addon:custom-event-name', {
  detail: { message: 'This is a custom event message' },
});
