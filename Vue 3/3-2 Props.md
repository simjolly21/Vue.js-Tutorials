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

We declare long prop names using camelCase because this avoids having to use quotes when using them as property keys, and allows us to reference them directly in template expressions because they are valid JavaScript identifiers:

    defineProps({
        greetingMessage: String
    })

`

    <span>{{ greetingMessage }}</span>

Technically, you can also use camelCase when passing props to a child component (except in [in-DOM templates](https://vuejs.org/guide/essentials/component-basics.html#in-dom-template-parsing-caveats)). However, the convention is using kebab-case in all cases to align with HTML attributes:

    <MyComponent greeting-message="hello" />

We use [PascalCase for component tags](https://vuejs.org/guide/components/registration.html#component-name-casing) when possible because it improves template readability by differentiating Vue components from native elements. However, there isn't as much practical benefit in using camelCase when passing props, so we choose to follow each language's conventions.

### Static vs.Dynamic Props

So far, you've seen props passed as static values, like in:

    <BlogPost title="My journey with Vue" />

You've also seen props assigned dynamically with `v-bind` or its `:` shortcut, such as in:

    <!-- Dynamically assign the value of a variable -->
    <BlogPost :title="post.title" />

    <!-- Dynamically assign the value of a complex expression -->
    <BlogPost :title="post.title + ' by ' + post.author.name" />

### Passing Different Value Types

In the two examples above, we happen to pass string values, but any type of value can be passed to a prop.

#### Number

    <!-- Even though `42` is static, we need v-bind to tell Vue that -->
    <!-- this is a JavaScript expression rather than a string.       -->
    <BlogPost :likes="42" />

    <!-- Dynamically assign to the value of a variable. -->
    <BlogPost :likes="post.likes" />

#### Boolean

    <!-- Including the prop with no value will imply `true`. -->
    <BlogPost is-published />

    <!-- Even though `false` is static, we need v-bind to tell Vue that -->
    <!-- this is a JavaScript expression rather than a string.          -->
    <BlogPost :is-published="false" />

    <!-- Dynamically assign to the value of a variable. -->
    <BlogPost :is-published="post.isPublished" />

#### Array

    <!-- Even though the array is static, we need v-bind to tell Vue that -->
    <!-- this is a JavaScript expression rather than a string.            -->
    <BlogPost :comment-ids="[234, 266, 273]" />

    <!-- Dynamically assign to the value of a variable. -->
    <BlogPost :comment-ids="post.commentIds" />

#### Object

    <!-- Even though the object is static, we need v-bind to tell Vue that -->
    <!-- this is a JavaScript expression rather than a string.             -->
    <BlogPost
        :author="{
            name: 'Veronica',
            company: 'Veridian Dynamics'
        }"
    />

    <!-- Dynamically assign to the value of a variable. -->
    <BlogPost :author="post.author" />

### Binding Multiple Properties Using an Object

If you want to pass all the properties of an object as props, you can use [`v-bind` without an argument](https://vuejs.org/guide/essentials/template-syntax.html#dynamically-binding-multiple-attributes) (`v-bind` instead of `:prop-name`). For example, given a `post` object:

    const post = {
        id: 1,
        title: 'My Journey with Vue'
    }

The following template:

    <BlogPost v-bind="post" />

Will be equivalent to:

    <BlogPost :id="post.id" :title="post.title" />

## One-Way Data Flow

All props form a one-way-down binding between the child property and the parent one: when the parent property updates, it will flow down to the child, but not the other way around. This prevents child components from accidentally mutating the parent's state, which can make your app's data flow harder to understand.

In addition, every time the parent component is updated, all props in the child component will be refreshed with the latest value. This means you should not attempt to mutate a prop inside a child component. If you do, Vue will warn you in the console:

    const props = defineProps(['foo'])

    // ❌ warning, props are readonly!
    props.foo = 'bar'

There are usually two cases where it's tempting to mutata a prop:

1. **The prop is used to pass in an initial value; the child component wants to use it as a local data property afterwards.** In this case, it's best to define a local data property that uses the prop as its initial value:


        const props = defineProps(['initialCounter'])

        // counter only uses props.initialCounter as the initial value;
        // it is disconnected from future prop updates.
        const counter = ref(props.initialCounter)

2. **The prop is passed in as a raw value that needs to be transformed.** In this case, it's best to define a computed property using the prop's value:


        const props = defineProps(['size'])

        // computed property that auto-updates when the prop changes
        const normalizedSize = computed(() => props.size.trim().toLowerCase())

## Mutating Object / Array Props

When objects and arrays are passed as props, while the child component cannot mutate the prop binding, it will be able to mutate the object or array's nested properties. This is because in JavaScript objects and arrays are passed by reference, and it is unreasonably expensive for Vue to prevent such mutations.

The main drawback of such mutations is that it allows the child component to affect parent state in a way that isn't obvious to the parent component, potentially making it more difficult to reason about the data flow in the future. As a best practice, you should avoid such mutations unless the parent and child are tightly coupled by design. In most cases, the child should [emit an event](https://vuejs.org/guide/components/events.html) to let the parent perform the mutation.


## Prop Validation

