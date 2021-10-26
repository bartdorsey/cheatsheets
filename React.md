# React CheatSheet

- [React CheatSheet](#react-cheatsheet)
  - [JSX](#jsx)
    - [JSX is really React.createElement](#jsx-is-really-reactcreateelement)
    - [Use curly braces to evaluate a javascript _expression_](#use-curly-braces-to-evaluate-a-javascript-expression)
      - [You can't use `if`, or `for` loops inside JSX, they are not _expressions_](#you-cant-use-if-or-for-loops-inside-jsx-they-are-not-expressions)
    - [Use map with JSX to render collections](#use-map-with-jsx-to-render-collections)
      - [Tip: to avoid many nested parens and curlies, use a variable](#tip-to-avoid-many-nested-parens-and-curlies-use-a-variable)
      - [Tip: Always remember to include a key property when rendering a collection](#tip-always-remember-to-include-a-key-property-when-rendering-a-collection)
      - [*IMPORTANT*: Your component will likely render without any state the first time](#important-your-component-will-likely-render-without-any-state-the-first-time)
        - [Use an empty array as initial state when using map](#use-an-empty-array-as-initial-state-when-using-map)
        - [Use a guard clause to avoid rendering](#use-a-guard-clause-to-avoid-rendering)
  - [useState Hook](#usestate-hook)
    - [Defining a piece of state](#defining-a-piece-of-state)
    - [the _set_ function only QUEUES an update](#the-set-function-only-queues-an-update)
    - [the _set_ function can _optionally_ take a callback function](#the-set-function-can-optionally-take-a-callback-function)
  - [useEffect Hook](#useeffect-hook)
    - [Components must be PURE functions](#components-must-be-pure-functions)
    - [Don't call the `set` function of useState inside a component.](#dont-call-the-set-function-of-usestate-inside-a-component)
    - [useEffect takes a callback as it's first argument, and a dependency array as it's second argument](#useeffect-takes-a-callback-as-its-first-argument-and-a-dependency-array-as-its-second-argument)
    - [The dependency array](#the-dependency-array)
    - [Do use useEffect for side effects](#do-use-useeffect-for-side-effects)
    - [When to not use useEffect](#when-to-not-use-useeffect)
  - [Event Handling in React](#event-handling-in-react)
    - [Events are added to JSX and passed a callback](#events-are-added-to-jsx-and-passed-a-callback)
    - [Don't call the function in the onClick attribute](#dont-call-the-function-in-the-onclick-attribute)
  - [Forms](#forms)
    - [Make your forms controlled components](#make-your-forms-controlled-components)
    - [Use an onSubmit event on the form tag and remember to prevent default behavior](#use-an-onsubmit-event-on-the-form-tag-and-remember-to-prevent-default-behavior)

---

## JSX

### JSX is really React.createElement

```jsx
<div id="my-div">Hello World</div>
// React.createElement('div', { id: "my-div" }, "Hello World");
```

### Use curly braces to evaluate a javascript _expression_

```jsx
const myId = 'my-div';
const content = 'Hello World'

<div id={myId}>{content}</div>
```

#### You can't use `if`, or `for` loops inside JSX, they are not _expressions_

```jsx
<div>{ if (true) "Hello World" }</div>
```

```jsx
<div>
    {
        for (const item of items) {
            ...
        }
    }
</div>
```

### Use map with JSX to render collections

```jsx
<div id="items">
  {items.map((item) => (
    <Item key={item.id} item={item} />
  ))}
</div>
```

#### Tip: to avoid many nested parens and curlies, use a variable

```jsx
const itemComponents = items.map(item =>
    <Item key={item.id} item={item}>
);

<div>{itemComponents}</div>
```

#### Tip: Always remember to include a key property when rendering a collection

```jsx
// Remember the key should be a unique value
const itemComponents = items.map(item =>
    <Item key={item.id} item={item}>
);
```

#### *IMPORTANT*: Your component will likely render without any state the first time

This is the most common problem new React developers run into. When you are fetching remote data, your component will first render with no data. So write your code in your component _defensively_ to avoid this.

##### Use an empty array as initial state when using map

```jsx
const [items, setItem] = useState(); 
// Whoops, we have undefined state ^^^
// We really should use an empty array:
// useState([])

// This generates an error because we can't
// call map on an undefined value of items.
const itemComponents = items.map(item => {
    <Item key={item.id} item={item}/>
});
```

##### Use a guard clause to avoid rendering

Sometimes if you have no data, it's better to just
render nothing, or a message to the user that the component is
"loading".

```jsx
const MyComponent = () => {
    const [items, setItems] = useState([]);

    // This is a guard clause, it returns from our
    // component early if our items array is empty.
    if (items.length === 0) {
        return null;
        // or you might return a loading message
        // return <div>Loading...</div>
    }

    return (
        // Return our normal JSX here.
    )
}
```

---

## useState Hook

### Defining a piece of state

useState returns an array with two elements, the state itself, and a function
to update the state. It accepts the initial value for the state as an argument

```jsx
const [thing, setThing] = useState(initialState)
```

### the _set_ function only QUEUES an update

If you try to access the state after you've changed it,
it won't have changed yet. This is because the set function
only queues up a change, it doesn't happen right away.

```jsx
const [count, setCount] = useState(0)

// ...later in the component
setCount(count + 1)
console.log(count) // this will still be 0.
// On the next render, count will be 1.
```

### the _set_ function can _optionally_ take a callback function

Whatever we return from the callback will be the new state.
It gets passed the previous state value as the first argument.

```jsx
const [count, setCount] = useState(0)

setCount((prevState) => {
  return count + 1
})
```

---

## useEffect Hook

### Components must be PURE functions

They must return JSX or null and not interact with anything outside of the
function.

```jsx
const MyComponent = () => {
  const [data, setData] = useState()
  // You can't do this! It's a side effect!
  fetch(url)
    .then((response) => response.json())
    .then((data) => setData(data))
}
```

### Don't call the `set` function of useState inside a component.

It should always be called in a useEffect or in an event handler.

```jsx
const MyComponent = () => {
  const [message, setMessage] = useState()

  setData("Hello World") // This is a side effect!
  // It will actually trigger an endless loop of rendering!
}
```

### useEffect takes a callback as it's first argument, and a dependency array as it's second argument

```jsx
useEffect(() => {
  // side effect code goes here
}, []) // This is the dependency array
```

### The dependency array

The dependency array decides WHEN the useEffect callback will run.

- `undefined` - The callback will run everytime
- `[]` - The callback will run only on the first render.
- `[somevariable]` - The callback will run on the first render and anytime `somevariable` changes.

### Do use useEffect for side effects

Common side effects we should put in a useEffect Hook:

- Fetch Calls
- Reading or writing to localStorage
- Reading or writing Cookies
- Accessing a global variable (you should avoid this anyway)

### When to not use useEffect

- When you are just doing rendering logic or calculating a value
- When you have an event listener like a click or submit

---

## Event Handling in React

### Events are added to JSX and passed a callback

In DOM you might do this:

```js
button.addEventListener("click", (event) => {
  // Do something with the event.target
})
```

In React you add them using attributes on the JSX

```jsx
return (
  <button
    onClick={(event) => {
      // Do something with the event.target
    }}
  >
    Click Me!
  </button>
)
```

It's cleaner to define your event callback externally to the JSX

```jsx
const handleClick = (event) => {
  // Do something with the event.target
}

return <button onClick={handleClick}>Click Me!</button>
```

### Don't call the function in the onClick attribute

```jsx
// This will run the handleClick right away, instead of waiting
// for the click to happen.
return <button onClick={handleClick()}>Click Me!</button>
```

---

## Forms

### Make your forms controlled components

This means adding an onChange listener to each form control and storing
state locally for each control.

```jsx
const [name, setName] = useState("")

return (
  <form>
    <input 
        name="name" 
        value={name} 
        onChange={(e) => setName(e.target.value)} />
    <button>Submit</button>
  </form>
)
```

### Use an onSubmit event on the form tag and remember to prevent default behavior

```jsx
const handleSubmit = e => {
    e.preventDefault();
    // Do something with the data 
    // from the form
}

return (
    <form onSubmit={handleSubmit}>
    ...
    </form>
)
```
