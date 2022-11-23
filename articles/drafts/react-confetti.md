[`canvas-confetti`](https://www.npmjs.com/package/canvas-confetti) is a popular vanilla-javascript library.

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


#### Expose API through a ref

From the parent, we'd like to manually trigger the `fire` function, eg, when clicking on a button:

```jsx
const confettiRef = useRef()

<Confetti ref={confettiRef} manualstart />

<button onClick={() => confettiRef.current.fire({ particleCount: Math.round(Math.random()*100) }) }>
```

For this, [`forwardRef`](https://reactjs.org/docs/forwarding-refs.html) will allow us to pass this `confettiRef` to the component, and [`useImperativeHandle`](https://reactjs.org/docs/hooks-reference.html#useimperativehandle) to attach an api object to it, exposing `fire`:

```jsx
const Confetti = forwardRef((props, ref) => {
  // ...
  
  // expose an API object to the forwarded ref
  useImperativeHandle(ref, () => ({
    fire
  }), [fire]);
  
  useEffect(() => {
    if (!manualstart) {
      fire(); // fire unless `manualstart` prop is true
    }
  }, [manualstart, fire]);
  
  // ...
})
```

#### Typing

