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
