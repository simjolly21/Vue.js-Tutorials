# Props

## Props Declaration

Vue components require explicit props declaration so that Vue knows what external props passed to the component should be treated as fallthrough attributes (which will be discussed in [its dedicated section](https://vuejs.org/guide/components/attrs.html)).

In SFCs using `<script setup>`, props can be declared using the `defineProps()` macro:


    <script setup>
    const props = defineProps(['foo'])

    console.log(props.foo)
    </script>

In non-`<script setup>` components, props are declared using the `props` option:

    export default {
        props: ['foo'],
        setup(props) {
            // setup() receives props as the first argument.
            console.log(props.foo)
        }
    }

Notice the argument passed to `defineProps()` is the same as the value provided to the `props` options: the same props options API is shared between the two declaration styles.

In addition to declaring props using an array of strings, we can also use the object syntax:

    // in <script setup>
    defineProps({
        title: String,
        likes: Number
    })

`

    // in non-<script setup>
    export default {
        props: {
            title: String,
            likes: Number
        }
    }

For each property in the object declaration syntax, the key is the name of the prop, while the value should be the constructor function of the expected type.

This not only documents your component, but will also warn other developers using your component in the browser console if they pass the wrong type. We will discuss more details about [prop validation](https://vuejs.org/guide/components/props.html#prop-validation) further down this page.

If you are using TypeScript with `<script setup>`, it's also possible to declare props using pure type annotations:

    <script setup lang="ts">
    defineProps<{
        title?: string
        likes?: number
    }>()
    </script>

More details: [Typing Component Props](https://vuejs.org/guide/typescript/composition-api.html#typing-component-props) <sup>`TS`</sup>

## Reactvie Props Destructure   <sup>`3.5+`</sup>

Vue's reactivity system tracks state usage based on property access. E.g. when you access `props.foo` in a computed getter or a watcher, the `foo` prop gets tracked as a dependency.

So, given the following code:

    const { foo } = defineProps(['foo'])

    watchEffect(() => {
        // runs only once before 3.5
        // re-runs when the "foo" prop changes in 3.5+
        console.log(foo)
    })

In version 3.4 and below, `foo` is an actual constant and will never change. In version 3.5 and above, Vue's compiler automatically prepends `props`. when code in the same `<script setup>` block accesses variables destructured from `defineProps`. Therefore the code above becomes equivalent to the following:

    const props = defineProps(['foo'])

    watchEffect(() => {
        // `foo` transformed to `props.foo` by the compiler
        console.log(props.foo)
    })

In addition, you can use JavaScript's native default value syntax to declare default values for the props. This is particularly useful when using the type-based props declaration:

    const { foo = 'hello' } = defineProps<{ foo?: string }>()

If you prefer to have more visual distinction between destructured props and normal variables in your IDE, Vue's VSCode extension provides a setting to enable inlay-hints for destructured props.

### Passing Desctructured Props into Functions

When we pass a destructured prop into a function, e.g.:

    const { foo } = defineProps(['foo'])

    watch(foo, /* ... */)

This will not work as expected because it is equivalent to `watch(props.foo, ...)` - we are passing a value instead of a reactive data source to `watch`. In fact, Vue's compiler will catch such cases and throw a warning.

Similar to how we can watch a normal prop with `watch(() => props.foo, ...)`, we can watch a destructured prop also by wrapping it in a getter:

    watch(() => foo, /* ... */)

In addition, this is the recommended approach when we need to pass a destructured prop into an external function while retaining reactivity:

    useComposable(() => foo)

The external function can call the getter (or normalize it with [toValue](https://vuejs.org/api/reactivity-utilities.html#tovalue)) when it needs to track changes of the provided prop, e.g. in a computed or watcher getter.

## Prop Passing Details

### Prop Name Casing