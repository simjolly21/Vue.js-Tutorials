# Form Input Bindings

When dealing with forms on the frontend, we often need to sync the state of form input elements with corresponding state in JavaScript. It can be cumbersome to manually wire up value bindings and change event listeners:

    <input
        :value="text"
        @input="event => text = event.target.value">

The `v-model` directive helps us simplify the above to:

    <input v-model="text">

In addition, `v-model` can be used on inputs of different types, `<textarea>`, and `<select>` elements. It automatically expands to different DOM property and event pairs based on the element it is used on:

- `<input>` with text types and `<textarea>` elements use `value` property and `input` event;
- `<input type="checkbox">` and `<input type="radio">` use `checked` property and change event;
- `<select>` uses `value` as a prop and `change` as an event.

## Basic Usage

### Text

    <p>Message is: {{ message }}</p>
    <input v-model="message" placeholder="edit me" />

### Mulitiline text

    <span>Multiline message is:</span>
    <p style="white-space: pre-line;">{{ message }}</p>
    <textarea v-model="message" placeholder="add multiple lines"></textarea>

Note that interpolation inside `<textarea>` won't work. Use `v-model` instead.

### Checkbox

Single checkbox, boolean value:

    <input type="checkbox" id="checkbox" v-model="checked" />
    <label for="checkbox">{{ checked }}</label>


We can also bind multiple checkboxes to the same array or Set value:

    const checkedNames = ref([])

    <div>Checked names: {{ checkedNames }}</div>

    <input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
    <label for="jack">Jack</label>

    <input type="checkbox" id="john" value="John" v-model="checkedNames" />
    <label for="john">John</label>

    <input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
    <label for="mike">Mike</label>


In this case, the `checkedNames` array will always contain the values from the currently checked boxes.

### Radio

    <div>Picked: {{ picked }}</div>

    <input type="radio" id="one" value="One" v-model="picked" />
    <label for="one">One</label>

    <input type="radio" id="two" value="Two" v-model="picked" />
    <label for="two">Two</label>

### Select

Single select:

    <div>Selected: {{ selected }}</div>

    <select v-model="selected">
        <option disabled value="">Please select one</option>
        <option>A</option>
        <option>B</option>
        <option>C</option>  
    </select>

Multiple select (bound to array):

    <div>Selected: {{ selected }}</div>

    <select v-model="selected" multiple>
        <option>A</option>
        <option>B</option>
        <option>C</option>
    </select>

Select options can be dynamically rendered with `v-for`:

    const selected = ref('A')

    const options = ref([
        { text: 'One', value: 'A' },
        { text: 'Two', value: 'B' },
        { text: 'Three', value: 'C' }
    ])

`

    <select v-model="selected">
        <option v-for="option in options" :value="option.value">
            {{ option.text }}
        </option>
    </select>

    <div>Selected: {{ selected }}</div>

