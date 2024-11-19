# List Rendering

## `v-for`

We can use the `v-for` directive to render a list of items based on an array. The v-for directive requires a special syntax in the form of `item in items`, where `items` is the source data array and `item` is an alias for the array element being iterated on:

    const items = ref([{ message: 'Foo'}, { message: 'Bar'}])

`

    <li v-for="item in items">
        {{ item.message }}
    </li>

Inside the `v-for` scope, template expressions have access to all parent scope properties. In addition, `v-for` also supports an optional second alias for the index of the current item:

    const parentMessage = ref('Parent')
    const items = ref([{ message: 'Foo'}, { message: 'Bar'}])

`

    <li v-for="(item, index) in items">
        {{ parentMessage }} - {{ index }} - {{ item.message }}
    </li>

- Parent - 0 - Foo
- Parent - 1 - Bar

The variable scoping of `v-for` is similar to the following JavaScript:

    const parentMessage = 'Parent'
    const items = [
        /* ... */
        ]

        items.forEach((item, index) => {
        // has access to outer scope `parentMessage`
        // but `item` and `index` are only available in here
        console.log(parentMessage, item.message, index)
    })

Notice how the `v-for` value matches the function signature of the `forEach` callback. In fact, you can use destructuring on the `v-for` item alias similar to destructuring function arguments:

    <li v-for="{ message } in items">
        {{ message }}
    </li>

    <!-- with index alias -->
    <li v-for="({ message }, index) in items">
        {{ message }} {{ index }}
    </li>

For nested `v-for`, scoping also works similar to nested functions. Each `v-for` scope has access to parent scopes:

    <li v-for="item in items">
        <span v-for="childItem in item.children">
            {{ item.message }} {{ childItem }}
        </span>
    </li>

You can also use `of` as the delimiter instead of `in`, so that it is closer to JavaScript's syntax for iterators:

    <div v-for="item of items"></div>


## `v-for` with an Object

You can also use `v-for` to iterate through the properties of an object. The iteration order will be based on the result of calling `Object.values()` on the object:

    const myObject = reactive({
        title: 'How to do lists in Vue',
        author: 'Jane Doe',
        publishedAt: '2016-04-10'
    })

`

    <ul>
        <li v-for="value in myObject">
            {{ value }}
        </li>
    </ul>

You can also provide a second alias for the property's name (a.k.a. key):

    <li v-for="(value, key) in myObject">
        {{ key }}: {{ value }}
    </li>

And another for the index:

    <li v-for="(value, key, index) in myObject">
        {{ index }}. {{ key }}: {{ value }}
    </li>

## `v-for` with a Range

`v-for` can also take an integer. In this case it will repeat the template that many times, based on a range of `1...n`.

    <span v-for="n in 10">{{ n }}</span>

Note here `n` starts with an initial value of `1` instead of `0`.

## `v-for` on `<template>`

Similar to template `v-if`, you can also use a `<template>` tag with `v-for` to render a block of multiple elements. For example:

    <ul>
        <template v-for="item in items">
            <li>{{ item.msg }}</li>
            <li class="divider" role="presentation"></li>
        </template>
    </ul>

## `v-for` with `v-if`

When they exist on the same node, `v-if` has a higher priority than `v-for`. That means the `v-if` condition will not have access to variables from the scope of the `v-for`:

    <!--
    This will throw an error because property "todo"
    is not defined on instance.
    -->
    <li v-for="todo in todos" v-if="!todo.isComplete">
        {{ todo.name }}
    </li>

This can be fixed by moving `v-for` to a wrapping `<template>`tag (which is also more explicit):

    <template v-for="todo in todos">
        <li v-if="!todo.isComplete">
            {{ todo.name }}
        </li>
    </template>

## Maintaining State with `key`

When Vue is updating a list of elements rendered with `v-for`, by default it uses an "in-place patch" strategy. If the order of the data items has changed, instead of moving the DOM elements to match the order of the items, Vue will patch each element in-place and make sure it reflects what should be rendered at that particular index.

This default mode is efficient, but **only suitable when your list render output does not rely on child component state or temporary DOM state (e.g. form input values)**.

To give Vue a hint so that it can track each node's identity, and thus reuse and reorder existing elements, you need to provide a unique `key` attribute for each item:

    <div v-for="item in items" :key="item.id">
        <!-- content -->
    </div>

When using `<template v-for>`, the `key` should be placed on the `<template>` container:

    <template v-for="todo in todos" :key="todo.name">
        <li>{{ todo.name }}</li>
    </template>

It is recommended to provide a `key` attribute with `v-for` whenever possible, unless the iterated DOM content is simple (i.e. contains no components or stateful DOM elements), or you are intentionally relying on the default behavior for performance gains.

The `key` binding expects primitive values - i.e. strings and numbers. Do not use objects as` v-for` keys. For detailed usage of the `key` attribute, please see the [`key` API documentation](https://vuejs.org/api/built-in-special-attributes.html#key).

## `v-for` with a Component

