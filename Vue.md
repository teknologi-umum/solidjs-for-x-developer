# Solid for Vue developer

There is [an article on the documentation](https://www.solidjs.com/docs/latest/api#vue) comparing Vue to Solid.

## Table of contents
* [Templates](#templates)
  * [Template strings](#template-strings)
  * [Conditional rendering](#conditional-rendering)
  * [List rendering](#list-rendering)
* [Props](#props)
* [Data](#data)
  * [v-model](#bindings)
* [Computed properties](#computed-properties)
* [Methods](#methods)
* [Events](#events)
  * [Event modifiers](#event-modifiers)
* [Lifecycle](#lifecycle) 
* Watchers (soon)
* Slots & scoped slots (soon)
* Provide / inject (soon)
* Custom directives (soon)
* Transitions (soon)

## Templates

### Template strings

**Vue**
```html
<template>
  <p>Hello, {{ name }}!</p>
</template>

<script>
export default {
  props: ['name']
};
</script>
```

**Solid**
```jsx
export default function Greeter(props) {
  return (
    <p>Hello, {props.name}!</p>
  );
}
```

### Conditional rendering

**Vue**
```html
<template>
  <article>
    <h1 v-if="awesome">Vue is awesome!</h1>
    <h1 v-else>Vue is still awesome, though.</h1>
  </article>
</template>

<script>
export default {
  props: ['awesome']
};
</script>
```

**Solid**
```jsx
import { Show } from 'solid-js' 

export default function Awesome(props) {
  return (
    <Show when={props.awesome} fallback={<h1>Solid is still awesome, though.</h1>}>
      <h1>Solid is awesome!</h1>
    </Show>
  )
}
```

[Documentation](https://www.solidjs.com/docs/latest/api#%3Cshow%3E)

### List rendering

**Vue**
```html
<template>
  <ul>
    <li v-for="(ingredient, index) in ingredients" :key="index">
      {{ ingredient }}
    </li>
  </ul>
</template>

<script>
export default {
  props: ['ingredients']
};
</script>
```

**Solid**
```jsx
import { For } from 'solid-js'

export default function Recipe(props) {
  return (
    <ul>
      <For each={props.ingredients}>
        {(ingredient, index) => (
          <li>{ingredient}</li>
        )}
      </For>
    </ul>
  )
}
```

[Documentation](https://www.solidjs.com/docs/latest/api#%3Cfor%3E)

## Props

Props behaves like Vue.

**Vue**
```html
<template>
  <h1>{{ title }}</h1>
  <description :text="description" />
</template>

<script>
export default {
  props: ['title', 'description'],
};
</script>
```

**Solid**
```jsx
export default function Post(props) {
  return (
    <h1>{props.title}</h1>
    <description text={props.description}></description>
  )
}
```

## Data

In Vue the `data` option is used to store local component state. In Solid, you can use [createSignal](https://www.solidjs.com/docs/latest/api#createsignal) to achive similar result.

**Vue**
```html
<template>
  <button @click="count++">
    You clicked me {{ count }} times.
  </button>
</template>

<script>
export default {
  data() {
    return {
      count: 0
    }
  }
};
</script>
```

**Solid**
```jsx
import { createSignal } from 'solid-js'

export default function Counter() {
  const [getCounter, setCounter] = createSignal(0)
  return (
    <button onClick={setCounter((previous) => previous + 1)}>
      You clicked me {getCounter()} times.
    </button>
  )
}
```

### Bindings

Solid doesn't have an equivalent tool for `v-model`. You could use explicit `onChange`.

**Vue**
```html
<template>
  <input type="text" v-model="name" />
</template>

<script>
export default {
  data() {
    return {
      name: 'Sebastian'
    }
  }
};
</script>
```

**Solid**
```jsx
import { createSignal } from 'solid-js'

export default function Profile() {
  const [getName, setName] = createSignal('Sebastian')
  return (
    <input type="text" value={getName()} onChange={event => setName(event.target.value)} />
  )
}
```

[Documentation](https://www.solidjs.com/docs/latest/api#createsignal)

Or you could use `use:___` and declare a function to be used.

```jsx
import { createSignal } from 'solid-js'

export default function Profile() {
  const [getName, setName] = createSignal('Sebastian')
  function model(element, value) {
    const [field, setField] = value();
    createRenderEffect(() => (element.value = field()));
    element.addEventListener('input', e => setField(e.target.value));
  }

  return (
    <input type="text" use:model={[getName, setName]} />
  )
}
```

[Documentation](https://www.solidjs.com/docs/latest/api#use%3A___)

## Computed properties

Vue has computed properties for two reasons: to avoid mixing logic and markup in templates, and to cache complex computations in a component instance.

**Vue**
```html
<template>
  <p>{{ reversedMessage }}</p>
</template>

<script>
export default {
  props: ['message'],

  computed: {
    reversedMessage() {
      return this.message.split('').reverse().join('');
    }
  }
};
</script>
```

**Solid**
```jsx
import { createMemo } from 'solid-js'

export function Message(props) {
  const reversedMessage = createMemo(() => props.message.split('').reverse().join(''))
  return (
    <p>{reversedMessage()}</p>
  )
}
```

[Documentation](https://www.solidjs.com/docs/latest/api#creatememo  )

## Methods

In Vue, you have to put it on the `methods` block. In Solid, it's pretty straightforward.

**Vue**
```html
<template>
  <button onClick="doSomething">
    Do something!
  </button>
</template>

<script>
export default {
  methods: {
    doSomething() {
      // ...
    }
  }
};
</script>
```

**Solid**
```jsx
export default function Something() {
  function doSomething() {
    // ...
  }

  return (
    <button onClick={doSomething()}>Do something</button>
  )
}
```

## Events

**Vue**
```html
<template>
  <form>
    <button type="button" @click="$emit('save')">
      Save
    </button>
    <button type="button" @click="$emit('publish')">
      Publish
    </button>
  </form>
</template>
```

**Solid**
```jsx
export default function PostForm(props) {
  return (
    <form>
      <button type="button" onClick={props.save()}>
        Save
      </button>
      <button type="button" onClick={props.publish()}>
        Publish
      </button>
    </form>
  )
}
```

[Documentation](https://www.solidjs.com/docs/latest/api#on___)

### Event modifiers

**Vue**
```html
<template>
  <form @submit.prevent="submitWithAjax">
    <!-- ... -->
  </form>
</template>

<script>
export default {
  methods: {
    submitWithAjax() {
      // ...
    }
  }
};
</script>
```

**Solid**
```jsx
export default function Form() {
  function submitWithAjax(e) {
    e.preventDefault()
    // ...
  }
  return (
    <form onSubmit={event => submitWithAjax(event)}>
      {/* ... */}
    </form>
  )
}
```

## Lifecycle

Vue has its' own [lifecycle hooks](https://v3.vuejs.org/api/options-lifecycle-hooks.html) (beforeCreate, created, beforeMount, etc..).

As for Solid's lifecycle is separated into 3 parts:

1. Computation Phase
  * `createMemo`
  * `createComputed`
2. Render Phase
  * `createRenderEffect`
  * `ref` are assigned
3. Post Render (new signal writes are batched)
  * `onMount`
  * `createEffect`

`onCleanup` is fired before the enclosing scope re-executes or when it is finally released.