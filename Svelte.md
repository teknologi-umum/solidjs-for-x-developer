# Solid for Svelte developer

There is [an article on the documentation](https://www.solidjs.com/docs/latest/api#svelte) comparing Svelte to Solid.

## Table of contents
* [Templates](#templates)
  * [Template strings](#template-strings)
  * [Conditional rendering](#conditional-rendering)
  * [List rendering](#list-rendering)
* [Props](#props)
* [Data](#data)
  * [v-model](#v-model)
* [Events](#events)
  * [Event modifiers](#event-modifiers)
* [Lifecycle](#lifecycle)
* Transitions (soon)
* Animations (soon)

## Templates

### Template strings

**Svelte**
```html
<script>
  export let name
</script>

<p>Hello, {name}</p>
```

**Solid**
```jsx
export default function Greeter({ name }) {
  return (
    <p>Hello, {name}!</p>
  );
}
```

### Conditional rendering

**Svelte**
```html
<script>
  export let awesome
</script>

{#if awesome}
  <h1>Svelte is awesome!</h1>
{:else}
  <h1>Solid is still awesome</h1>
{/if}
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

**Svelte**
```html
<script>
  export let ingredients
</script>

<ul>
  {#each ingredients as ingredient, index}
    <li>{ingredient}</li>
  {/each}
</ul>
```

**Solid**
```jsx
import { For } from 'solid-js'

export default function Recipe(props) {
  return (
    <For each={props.ingredients}>
      {(ingredient, index) => (
        {ingredient}
      )}
    </For>
  )
}
```

[Documentation](https://www.solidjs.com/docs/latest/api#%3Cfor%3E)

## Props

**Svelte**
```html
<script>
  export let title
  export let description
</script>

<h1>{title}</h1>
<description bind:text={description}></description>
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

**Svelte**
```html
<script>
  let counter = 0
</script>

<button on:click={() => counter++}>
  You clicked me {counter} times.
</button>
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

**Svelte**
```html
<script>
  let name = 'Sebastian'
</script>

<input type="text" bind:value={name} />
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

## Events

**Svelte**
```html
<form>
  <button type="button" on:click={() => save()}>
    Save
  </button>
  <button type="button" on:click={() => publish()}></button>
</form>
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

**Svelte**
```html
<form on:submit|preventDefault={submitWithAjax}></form>
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

Svelte has its' own [lifecycle functions](https://svelte.dev/tutorial/onmount) (onMount, beforeUpdate, afterUpdate, onDestroy, tick).

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