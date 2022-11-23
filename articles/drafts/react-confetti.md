[`canvas-confetti`](https://www.npmjs.com/package/canvas-confetti) is a popular vanilla-javascript library.

## Javascript API

```html
<canvas id="myCanvas"></canvas>

<script type="module">
import confetti from "https://cdn.skypack.dev/canvas-confetti@1.6.0";

const myCanvas = document.getElementById('myCanvas')

const myConfetti = confetti.create(myCanvas); // instance
myConfetti();
</script>
```
[Codepen](https://codepen.io/abernier/pen/NWzYXwv)

This is the simplest form (without any arguments), the [API](https://github.com/catdad/canvas-confetti#api) allows passing parameters:

```js
const globalOptions = {
  resize: true,
  useWorker: true
}
const myConfetti = confetti.create(myCanvas, globalOptions);

const options = {
  particleCount: 100,
  colors: ['#0000ff', '#eeeeee', '#ff0000']
}
myConfetti(options);
```
[Codepen](https://codepen.io/abernier/pen/NWzYXey?editors=1010)

## React component

We would like this:

```jsx
<Confetti
  globalOptions={{resize: true, useWorkder: true}}
  options={{
    particleCount: 100,
    colors: ['#0000ff', '#eeeeee', '#ff0000']
  }}
/>
```

```jsx
import { useRef, useCallback, useEffect } from "react";
import confetti from "canvas-confetti";

const Confetti = (props) => {
  const { options, globalOptions, ...rest } = props;
  const instanceRef = useRef(null); // confetti instance

  // our <canvas> DOM ref
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

  // `fire` is a function that call the instance() with `opts` merged with `options`
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

[Codesandbox](https://codesandbox.io/s/gallant-elbakyan-vnr7bl?file=/src/Confetti.jsx)


## 

Now, we would like to manually start the `fire()`, when clicking on a button:

```jsx
const confettiRef = useRef()

<Confetti ref={confettiRef} manualstart />

<button onClick={() => confettiRef.current.fire({ particleCount: Math.round(Math.random()*100) }) }>
```

For this, we can use `forwardRef` and `useImperativeHandle`:

```jsx
const Confetti = forwardRef((props, ref) => {
  // ...
  
  // expose an object API
  useImperativeHandle(ref, () => ({
    fire
  }));
  
  useEffect(() => {
    if (!manualstart) {
      fire();
    }
  }, [manualstart, fire]);
  
  // ...
})
```

