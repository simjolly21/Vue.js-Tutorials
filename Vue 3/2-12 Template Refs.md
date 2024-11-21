# Template Refs

While Vue's declarative rendering model abstracts away most of the direct DOM operations for you, there may still be cases where we need direct access to the underlying DOM elements. To achieve this, we can use the special `ref` attribute:


    <input ref="input">

`ref` is a special attribute, similar to the `key` attribute discussed in the `v-for` chapter. It allows us to obtain a direct reference to a specific DOM element or child component instance after it's mounted. This may be useful when you want to, for example, programmatically focus an input on component mount, or initialize a 3rd party library on an element.

## Accessing the Refs

To obtain the reference with Composition API, we can use the [`useTemplateRef()`](https://vuejs.org/api/composition-api-helpers.html#usetemplateref)<sup>3.5+</sup>  helper:

    <script setup>
    import { useTemplateRef, onMounted } from 'vue'

    // the first argument must match the ref value in the template
    const input = useTemplateRef('my-input')

    onMounted(() => {
        input.value.focus()
    })
    </script>

    <template>
        <input ref="my-input" />
    </template>

When using TypeScript, Vue's IDE support and `vue-tsc` will automatically infer the type of `input.value` based on what element or component the matching `ref` attribute is used on.


Note that you can only access the ref after the component is mounted. If you try to access `input` in a template expression, it will be `null` on the first render. This is because the element doesn't exist until after the first render!

If you are trying to watch the changes of a template ref, make sure to account for the case where the ref has `null` value:

    watchEffect(() => {
        if (input.value) {
            input.value.focus()
        } else {
            // not mounted yet, or the element was unmounted (e.g. by v-if)
        }
    })

See also: [Typing Template Refs](https://vuejs.org/guide/typescript/composition-api.html#typing-template-refs) <sup>TS</sup>

## Refs inside `v-for`

When `ref` is used inside `v-for`, the corresponding ref should contain an Array value, which will be populated with the elements after mount:

    <script setup>
    import { ref, useTemplateRef, onMounted } from 'vue'

    const list = ref([
        /* ... */
    ])

    const itemRefs = useTemplateRef('items')

    onMounted(() => console.log(itemRefs.value))
    </script>

    <template>
        <ul>
            <li v-for="item in list" ref="items">
                {{ item }}
            </li>
        </ul>
    </template>

It should be noted that the ref array does **not** guarantee the same order as the source array.