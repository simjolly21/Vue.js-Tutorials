# Template Syntax

Vue uses an HTML-based template syntax that allows you to declaratively bind the rendered DOM to the underlying component instance's data. All Vue templates are syntactically valid HTML that can be parsed by spec-compliant browsers and HTML parsers.

Under the hood, Vue compiles the templates into highly-optimized JavaScript code. Combined with the reactivity system, Vue can intelligently figure out the minimal number of components to re-render and apply the minimal amount of DOM manipulations when the app state changes.

If you are familiar with Virtual DOM concepts and prefer the raw power of JavaScript, you can also [directly write render functions](https://vuejs.org/guide/extras/render-function.html) instead of templates, with optional JSX support. However, do note that they do not enjoy the same level of compile-time optimizations as templates.

## Text Interpolation

The most absic form of data binding is text interpolation using the "Mustache" syntax(double curly braces):

    <span>Message: {{ msg }}</span>
The mustache tag will be replaced with the value of the `msg` property [from the corresponding component instance](https://vuejs.org/guide/essentials/reactivity-fundamentals.html#declaring-reactive-state). It will also be updated whenever the `msg` property changes.

## Raw HTML

The double mustaches interpret the data as plain text, not HTML. In order to output real HTML, you will need to use the `v-html` [directive](https://vuejs.org/api/built-in-directives.html#v-html):

    <p>Using text interpolation: {{ rawHtml }}</p>
    <p>Using v-html directive: <span v-html="rawHtml"></span></p>
`

    Using text interpolation: <span style="color: red">This should be red.</span>

    Using v-html directive: This should be red.
Here we're encountering something new. The `v-html` attrivute you're seeing is called a directive. Directives are prefixed with `v-` to indicate that they are special attributes provided by Vue, and as you may have guessed, they apply special reactive behavior to the rendered DOM. Here, we're basically saying "keep this element's inner HTML up-to-date with the `rawHtml` property on the current active instance".

The contents of the `span` will be replaced with the value of the `rawHtml` property, interpreted as plain HTML-data bindings are ignored. Note that you cannot use `v-html` to compose template partials, because Vue is not a sting-based templating engine. Instead, components are preferred as the fundamental unit for UI reuse and composition.

## Attribute Bindings

Mustaches cannot be used inside HTML attributes. Instead, use a `v-bind` [diractive](https://vuejs.org/api/built-in-directives.html#v-bind):

    <div v-bind:id="dynamicId"></div>
The `v-bind` diractive instructs Vue to keep the element's `id` attribute in sync with the component's `dynamicId` property. If the bound value is `null` or `undefined`, then the attribute will be removed from the rendered element.

### Shorthand

Because `v-bind` is so commonly used, it has a dedicated shorthand syntax:

    <div :id="dynamicId"></div>

Attributes that start with `:` may look a bit different from normal HTML, but it is in fact a valid character for attribute names and all Vue-supported browsers can parse it correctly. In addition, they do not appear in the final rendered markup. The shorthand syntax is optional, but you will likely appreciate it when you learn more about its usage later.

    For the rest of the guide, we will be using the shorthand syntax in code examples, as that's the most common usage for Vue developers.

### Same-name Shorthand

- Only supported in 3.4+

If the attribute has the same name with the JavaScript value being bound, the syntax can be further shortened to omit the attribute value:

    <!-- same as :id="id" -->
    <div :id></div>

    <!-- this also works -->
    <div v-bind:id></div>

This is similar to the property shorthand syntax when declaring objects in JavaScript. Note this is a feature that is only available in Vue 3.4 and above.

### Boolean Attributes

[Boolean attributes](https://html.spec.whatwg.org/multipage/common-microsyntaxes.html#boolean-attributes) are attributes that can indicate true / false values by their presence on an element. For example, `disabled` is one of the most commonly used boolean attributes.

`v-bind` works a bit differently in this case:

    <button :disabled="isButtonDisabled">Button</button>

The `disabled` attribute will be included if `isButtonDisabled` has a [truthy value](https://developer.mozilla.org/en-US/docs/Glossary/Truthy). It will also be included if the value is an empty string, maintaining consistency with `<button disabled="">`. For other [falsy values](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) the attribute will be omitted.

### Dynamically Binding Multiple Attributes

If you have a JavaScript object representing multiple attributes that looks like this:

    const objectOfAttrs = {
        id: 'container',
        class: 'wrapper',
        style: 'background-color:green'
    }

You can bind them to a single element by using `v-bind` without an argument:

    <div v-bind="objectOfAttrs"></div>

