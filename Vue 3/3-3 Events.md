# Component Events

## Emitting and Listening to Events

A component can emit custom events directly in template expressions (e.g. in a `v-on` handler) using the built-in `$emit` method:

    <!-- MyComponent -->
    <button @click="$emit('someEvent')">Click Me</button>

The parent can then listen to it using `v-on`:

    <MyComponent @some-event="callback" />

The `.once` modifier is also supported on component event listeners:

    <MyComponent @some-event.once="callback" />

