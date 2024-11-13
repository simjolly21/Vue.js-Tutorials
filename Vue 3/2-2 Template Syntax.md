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

