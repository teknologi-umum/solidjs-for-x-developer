# Solid for React developer

It's pretty much the same thing. Here we'll cover some of the control flow available on Solid. There is [an article on the documentation](https://www.solidjs.com/docs/latest/api#react) comparing React to Solid.

## Table of contents
* [What's changed?](#whats-changed-)
* [Conditional rendering](#conditional-rendering)
* [List rendering](#list-rendering)

## What's changed?
* useState -> [createSignal](https://www.solidjs.com/docs/latest/api#createsignal)
* useEffect -> [createEffect](https://www.solidjs.com/docs/latest/api#createeffect)
* useMemo -> [createMemo](https://www.solidjs.com/docs/latest/api#creatememo)

## Conditional rendering

**React**
```jsx
export default function Awesome({ awesome }) {
  return (
    <article>
      {awesome ? (
        <h1>React is awesome!</h1>
      ) : (
        <h1>Oh no 😢</h1>
      )};
    </article>
  )
}
```

**Solid**
```jsx
import { Show } from 'solid-js' 

export default function Awesome({ awesome }) {
  return (
    <Show when={awesome} fallback={<h1>Solid is still awesome, though.</h1>}>
      <h1>Solid is awesome!</h1>
    </Show>
  )
}
```

[Documentation](https://www.solidjs.com/docs/latest/api#%3Cshow%3E)

## List rendering

**React**
```jsx
export default function Recipe({ ingredients }) {
  return (
    <ul>
      {ingredients.map((ingredient, index) => (
        <li key={index}>{ingredient}</li>
      ))}
    </ul>
  );
}
```

**Solid**

You could still use `ingredients.map()` like you'd do on React, but there's a helper component built in on Solid.

```jsx
import { For } from 'solid-js'

export default function Recipe({ ingredients }) {
  return (
    <ul>
      <For each={ingredients}>
        {(ingredient, index) => (
          <li>{ingredient}</li>
        )}
      </For>
    </ul>
  )
}
```

[Documentation](https://www.solidjs.com/docs/latest/api#%3Cfor%3E)