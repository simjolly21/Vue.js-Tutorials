# Watchers

## Basic Example

Computed properties allow us to declaratively compute derived values. However, there are cases where we need to perform "side effects" in reaction to state changes - for example, mutating the DOM, or changing another piece of state based on the result of an async operation.

With Composition API, we can use the [`watch` function](https://vuejs.org/api/reactivity-core.html#watch) to trigger a callback whenever a piece of reactive state changes:

    <script setup>
    import { ref, watch } from 'vue'

    const question = ref('')
    const answer = ref('Questions usually contain a question mark. ;-)')
    const loading = ref(false)

    // watch works directly on a ref
    watch(question, async (newQuestion, oldQuestion) => {
        if (newQuestion.includes('?')) {
            loading.value = true
            answer.value = 'Thinking...'
            try {
            const res = await fetch('https://yesno.wtf/api')
            answer.value = (await res.json()).answer
            } catch (error) {
            answer.value = 'Error! Could not reach the API. ' + error
            } finally {
            loading.value = false
            }
        }
    })
    </script>

    <template>
        <p>
            Ask a yes/no question:
            <input v-model="question" :disabled="loading" />
        </p>
        <p>{{ answer }}</p>
    </template>

### Watch Source Types

`watch`'s first argument can be different types of reactive "sources": it can be a ref (including computed refs), a reactive object, a [getter function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get#description), or an array of multiple sources:

    const x = ref(0)
    const y = ref(0)

    // single ref
    watch(x, (newX) => {
        console.log(`x is ${newX}`)
    })

    // getter
    watch(
        () => x.value + y.value,
        (sum) => {
            console.log(`sum of x + y is: ${sum}`)
        }
    )

    // array of multiple sources
    watch([x, () => y.value], ([newX, newY]) => {
        console.log(`x is ${newX} and y is ${newY}`)
    })

Do note that you can't watch a property of a reactive object like this:

    const obj = reactive({ count: 0 })

    // this won't work because we are passing a number to watch()
    watch(obj.count, (count) => {
        console.log(`Count is: ${count}`)
    })

Instead, use a getter:

    // instead, use a getter:
    watch(
        () => obj.count,
        (count) => {
            console.log(`Count is: ${count}`)
        }
    )


## Deep Watchers

When you call `watch()` directly on a reactive object, it will implicitly create a deep watcher - the callback will be triggered on all nested mutations:

    const obj = reactive({ count: 0 })

    watch(obj, (newValue, oldValue) => {
        // fires on nested property mutations
        // Note: `newValue` will be equal to `oldValue` here
        // because they both point to the same object!
    })

    obj.count++

This should be differentiated with a getter that returns a reactive object - in the latter case, the callback will only fire if the getter returns a different object:

    watch(
        () => state.someObject,
        () => {
            // fires only when state.someObject is replaced
        }
    )

You can, however, force the second case into a deep watcher by explicitly using the `deep` option:

    watch(
        () => state.someObject,
        (newValue, oldValue) => {
            // Note: `newValue` will be equal to `oldValue` here
            // *unless* state.someObject has been replaced
        },
        { deep: true }
    )

In Vue 3.5+, the `deep` option can also be a number indicating the max traversal depth - i.e. how many levels should Vue traverse an object's nested properties.

## Eager Watchers

`watch` is lazy by default: the callback won't be called until the watched source has changed. But in some cases we may want the same callback logic to be run eagerly - for example, we may want to fetch some initial data, and then re-fetch the data whenever relevant state changes.

We can force a watcher's callback to be executed immediately by passing the `immediate: true` option:

    watch(
        source,
        (newValue, oldValue) => {
            // executed immediately, then again when `source` changes
        },
        { immediate: true }
    )

