A pretty common scenario in a React environment is to transpose a JS library to a React component...

[`canvas-confetti`](https://www.npmjs.com/package/canvas-confetti) is a popular vanilla-javascript library, with no React support.

## Javascript API

The simplest form is:

```html
<canvas id="myCanvas"></canvas>

<script type="module">
import confetti from "https://cdn.skypack.dev/canvas-confetti@1.6.0";

const myCanvas = document.getElementById('myCanvas')

const myConfetti = confetti.create(myCanvas); // instance
myConfetti();
</script>
```
We create a `myConfetti` instance, that is a function we can call later to fire confettis.

[Codepen](https://codepen.io/abernier/pen/NWzYXwv)

---

Reading the [API](https://github.com/catdad/canvas-confetti#api), we can also pass arguments when creating the instance:

```js
const globalOptions = {
  resize: true,
  useWorker: true
}
const myConfetti = confetti.create(myCanvas, globalOptions);
```

or when firing it:

```js
const options = {
  particleCount: 100,
  colors: ['#0000ff', '#eeeeee', '#ff0000']
}
myConfetti(options);
```
[Codepen](https://codepen.io/abernier/pen/NWzYXey?editors=1010)

## React component

Now we have the imperative API in mind, let's create a declarative React component of it.

### Signature

```jsx
<Confetti
  globalOptions={{resize: true, useWorkder: true}}
  options={{
    particleCount: 100,
    colors: ['#0000ff', '#eeeeee', '#ff0000']
  }}
/>
```

NB: `globalOptions` and `options` will be optional props

### Implementation

```jsx
import { useRef, useCallback, useEffect } from "react";
import confetti from "canvas-confetti";

const Confetti = (props) => {
  const { options, globalOptions, ...rest } = props;
  const instanceRef = useRef(null); // confetti instance

  //
  // confetti instance
  //

  const canvasRef = useCallback(
    (node) => {
      if (node !== null) {
        // <canvas> is mounted => create the confetti instance (if not already created)
        if (instanceRef.current) return;
        instanceRef.current = confetti.create(node, globalOptions);
      } else {
        // <canvas> is unmounted => reset and destroy instanceRef
        instanceRef.current.reset();
        instanceRef.current = undefined;
      }
    },
    [globalOptions]
  );

  //
  // `fire` is a function that call the instance function with `opts` merged with `options` prop
  //

  const fire = useCallback(
    (opts = {}) => {
      const instance = instanceRef.current;
      instance({
        ...options,
        ...opts
      });
    },
    [options]
  );

  useEffect(() => {
    fire();
  }, [fire]);

  return <canvas ref={canvasRef} {...rest} />;
};

export default Confetti;
```

NB: we use a `canvasRef` [callback ref](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs) here to get control over when the element is mounted/unmounted.

[Codesandbox](https://codesandbox.io/s/gallant-elbakyan-vnr7bl?file=/src/Confetti.jsx)


#### `fire` from parent

From the parent, we'd like to manually trigger the `fire` function, eg, when clicking on a button:

```jsx
const confettiRef = useRef()

<Confetti ref={confettiRef} manualstart />

<button onClick={() => confettiRef.current.fire({ particleCount: Math.round(Math.random()*100) }) }>
```

NB: `manualstart` is a new prop we introduce to disable auto-firing

For this, [`forwardRef`](https://reactjs.org/docs/forwarding-refs.html) will allow us to pass this `confettiRef` to the component, and [`useImperativeHandle`](https://reactjs.org/docs/hooks-reference.html#useimperativehandle) to attach an api object to it, exposing `fire`:

```jsx
const Confetti = forwardRef((props, ref) => {
  // ...
  
  //
  // expose an API object to the forwarded ref
  //
  
  const api = useMemo(() => ({
    fire
  }), [fire])

  useImperativeHandle(ref, () => api, [api]);
  
  //
  // Auto fire unless `manualstart`
  //

  useEffect(() => {
    if (!manualstart) {
      fire();
    }
  }, [manualstart, fire]);
  
  // ...
})
```

[Codesandbox](https://codesandbox.io/s/modest-phoebe-lztysi?file=/src/Confetti.jsx)

#### `fire` from descendant

```jsx
const ConfettiContext = createContext()

const Confetti = forwardRef((props, ref) => {

  // ...
  
  return (
    <ConfettiContext.Provider value={api}>
      <canvas ref={canvasRef} {...rest} />
      {children}
    </ConfettiContext.Provider>
  )
})

const useConfetti = () => useContext(ConfettiContext)
```

now we can `useConfetti()` in a descendant component to access its api:

```jsx
<Confetti manualstart>
  <Button>Fire</Button>
</Confetti>
```

```jsx
import { useConfetti } from "./Confetti";

const Button = () => {
  const { fire } = useConfetti();

  return (
    <button
      onClick={() => {
        fire();
      }}
    >
      Fire
    </button>
  );
};

export default Button;
```

[Codesandbox](https://codesandbox.io/s/silly-bessie-mky7lo?file=/src/Button.jsx:0-234)

#### Typing

##### Wrapping `canvas` element

```tsx
interface Props extends React.ComponentPropsWithRef<"canvas"> {
  // ...
}

const Confetti = (props: Props) => {
  const { /**/ , ...rest} = props
  
  // ...
  
  return <canvas {...rest} />
}
```

cf. https://stackoverflow.com/a/74562184/133327

##### 

[Codesandbox](https://codesandbox.io/s/nervous-panini-h6mlcm?file=/src/Confetti.tsx)
