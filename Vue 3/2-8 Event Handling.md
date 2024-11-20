# Event Handling

## Listening to Events

We can use the `v-on` directive, which we typically shorten to the `@` symbol, to listen to DOM events and run some JavaScript when they're triggered. The usage would be `v-on:click="handler"` or with the shortcut, `@click="handler"`.

The handler value can be one of the following:

1. **Inline handlers**: Inline JavaScript to be executed when the event is triggered (similar to the native `onclick` attribute).

2. **Method handlers**: A property name or path that points to a method defined on the component.

## Inline Handlers

Inline hadlers are typically used in simple cases, for example:

    const count = ref(0)

`

    <button @click="count++">Add 1</button>
    <p>Count is: {{ count }}</p>

## Method Handlers

The logic for many event handlers will be more complex though, and likely isn't feasible with inline handlers. That's why `v-on` can also accept the name or path of a component method you'd like to call.

For example:

    const name = ref('Vue.js')

    function greet(event) {
        alert(`Hello ${name.value}!`)
        // `event` is the native DOM event
        if (event) {
            alert(event.target.tagName)
        }
    }

`

    <!-- `greet` is the name of the method defined above -->
    <button @click="greet">Greet</button>

A method handler automatically receives the native DOM Event object that triggers it - in the example above, we are able to access the element dispatching the event via `event.target`.

See also: [Typing Event Handlers](https://vuejs.org/guide/typescript/composition-api.html#typing-event-handlers) <sup>`TS`</sup>

### Mehod vs.Inline Detection

The template compiler detects method handlers by checking whether the `v-on` value string is a valid JavaScript identifier or property access path. For example, `foo`, `foo.bar` and `foo['bar']` are treated as method handlers, while `foo()` and `count++` are treated as inline handlers.

## Calling Methods in Inline Handlers

Instead of binding directly to a method name, we can also call methods in an inline handler. This allows us to pass the method custom artuments instead of the native event:

    function say(message) {
        alert(message)
    }

`

    <button @click="say('hello')">Say hello</button>
    <button @click="say('bye')">Say bye</button>

## Accessing Event Argument in Inline Handlers

Sometimes we also need to access the original DOM event in an inline handler. You can pass it into a method using the special `$event` variable, or use an inline arrow function:

    <!-- using $event special variable -->
    <button @click="warn('Form cannot be submitted yet.', $event)">
        Submit
    </button>

    <!-- using inline arrow function -->
    <button @click="(event) => warn('Form cannot be submitted yet.', event)">
        Submit
    </button>

`

    function warn(message, event) {
        // now we have access to the native event
        if (event) {
            event.preventDefault()
        }
        alert(message)
    }

## Event Modifiers

It is a very common need to call `event.preventDefault()` or `event.stopPropagation()` inside event handlers. Although we can do this easily inside methods, it would be better if the methods can be purely about data logic rather than having to deal with DOM event details.

To address this problem, Vue provides event modifiers for `v-on`. Recall that modifiers are directive postfixes denoted by a dot.

- `.stop`
- `.prevent`
- `.self`
- `.capture`
- `.once`
- `.passive`


        <!-- the click event's propagation will be stopped -->
        <a @click.stop="doThis"></a>

        <!-- the submit event will no longer reload the page -->
        <form @submit.prevent="onSubmit"></form>

        <!-- modifiers can be chained -->
        <a @click.stop.prevent="doThat"></a>

        <!-- just the modifier -->
        <form @submit.prevent></form>

        <!-- only trigger handler if event.target is the element itself -->
        <!-- i.e. not from a child element -->
        <div @click.self="doThat">...</div>


The `.capture`, `.once`, and `.passive` modifiers mirror the [options of the native `addEventListener` method](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#options):

    <!-- use capture mode when adding the event listener     -->
    <!-- i.e. an event targeting an inner element is handled -->
    <!-- here before being handled by that element           -->
    <div @click.capture="doThis">...</div>

    <!-- the click event will be triggered at most once -->
    <a @click.once="doThis"></a>

    <!-- the scroll event's default behavior (scrolling) will happen -->
    <!-- immediately, instead of waiting for `onScroll` to complete  -->
    <!-- in case it contains `event.preventDefault()`                -->
    <div @scroll.passive="onScroll">...</div>

The `.passive` modifier is typically used with touch event listeners for [improving performance on mobile devices](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#improving_scroll_performance_using_passive_listeners).

## Key Modifiers

