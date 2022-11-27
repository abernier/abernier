THREE spaces
===

## "Space"

In a 3d environment, a "space" is defined by 3 perpendicular axes and an origin:
[![image](https://docs.google.com/drawings/d/e/2PACX-1vSgAWjz37yphQgR4NkrW8Lehtg3LAVJmqn0fMcGoMgxETzU1dj_V36MAPUfCwSFrtRkK3IEVsMzeWXE/pub?w=1440&h=1080)](https://docs.google.com/drawings/d/1thD_hICjAhERuMXal7ez8HhEk9JPYV7iF5Mhb2VIYCU/edit)

It allows us to locate any "point" in this space, by its 3 coordinates components:
[![image](https://docs.google.com/drawings/d/e/2PACX-1vQDwhYakAtd5OudiUgRExULy6C-LwD-KTuOe_k0DpkR3nAGWkj4LXOdYEt8f3rKgb7g3EA10YGq7EQu/pub?w=1440&h=1080)](https://docs.google.com/drawings/d/1kflQFDXOeOiT6VvMu_0fktmethV-jprDh9zLv8ZBshE/edit)

**In THREE, a such space is called an [`Object3D`](https://threejs.org/docs/index.html?q=object#api/en/core/Object3D).**

## World space

A default "world" space will always exist in a [`Scene`](https://threejs.org/docs/index.html?q=scene#api/en/scenes/Scene):
[![image](https://docs.google.com/drawings/d/e/2PACX-1vRG2Sxu2kkV8wW3c3spdxQ3GzsZfhmubzisEEEQzOjyuh0m0WqO97X5K2WI9UQXc7Qd08prGGxXnQxg/pub?w=1440&h=1080)](https://docs.google.com/drawings/d/1S81NPv6S1NSpr2A_qBdVaZaKk4l4LqN3I_6jQa0tJ_E/edit)
```js
const scene = new THREE.Scene()
```

Any other "object" in the scene has also its own "space", typically a [`Mesh`](https://threejs.org/docs/index.html?q=mesh#api/en/objects/Mesh) — which is an `Object3d`:
[![image](https://docs.google.com/drawings/d/e/2PACX-1vSfT3Ikc78bzVPFAhd-48AQpLx1sTAV48Tt4QkXjQR8znLOeS3scZfN5l1WVSeaymacfrcGCvqrqoAq/pub?w=1440&h=1080)](https://docs.google.com/drawings/d/10be51vBd848bzi2RwGcrRLCmRWqmhSwPFs8FR5kdKvQ/edit)
```js
const cube = new THREE.Mesh( /* ... */ )
scene.add(cube)
```

NB: A [`Camera`](https://threejs.org/docs/index.html?q=camera#api/en/cameras/Camera) is also an `Object3D` and has its own "space" too.

## Topology

`Scene` is the root of any "object":
```mermaid
graph TD;
  scene---table;
  scene---chair;
```

Objects can be nested together, thanks to [`Object3D.add()`](https://threejs.org/docs/index.html?q=matrix4#api/en/core/Object3D.add) method:

```mermaid
graph TD;
  scene---parent;
  parent---child;
```

This shapes a tree/hierarchy of spaces.

## Transforms

We can `position`, `rotate`, `scale` a such "space". An [`Object3D.matrix`](https://threejs.org/docs/index.html?q=matrix4#api/en/core/Object3D.matrix) property exists that holds all those transforms applied.

## Change of basis

Sometimes, we know the coordinates of a point in a given "space", but we want to express them in another "space"... That is what we call a [change of basis](https://en.wikipedia.org/wiki/Change_of_basis).

We represent this point by a `vec`.

### Math

[Mathematically](https://fr.wikipedia.org/wiki/Changement_de_base_(alg%C3%A8bre_lin%C3%A9aire)):

$$X=P_{B\rightarrow B'} . X'$$

or speaking with $V$ and $R1$, $R2$:

$$V_{R1}=P_{R1\rightarrow R2} . V_{R2}$$

Meaning that if we have the coordinates of a vector $V$ expressed in base $R2$, we can get its coordinates expressed in base $R1$ by multiplying this vector by the transformation matrix from $R1$ to $R2$: $P_{R1\rightarrow R2}$

And reciprocally (from $R1$ to $R2$):

$$V_{R2}=(P_{R1\rightarrow R2})^{-1} . V_{R1}$$

---

To summup:

```mermaid
graph TD;
  R1 -- P^-1 --> R2;
  R2 -- P --> R1;
```

ie, to express a vector:
- from $R1$ to $R2$, we multiply it by $P^{-1}$
- from $R2$ to $R1$, we multiply it by $P$

### A. Direct child/parent "spaces"

To know the coordinates of `vec` in the other "direct" space:

```mermaid
graph TD;
  parent -- "vec.applyMatrix4(child.matrix^-1)" --> child;
  child -- "vec.applyMatrix4(child.matrix)" --> parent;
```

### B. Any "world" descendant object

In each "space", a convenient `Object3D.matrixWorld` accumulates the transforms of all its ancestors, up to the `Scene` root.

It helps going from world <-> local space more easily (rather than recomputing the chain-matrices product ourselves):

```mermaid
graph TD;
  scene -- "vec.applyMatrix4(object.matrixWorld^-1)" --> object;
  object -- "vec.applyMatrix4(object.matrixWorld)" --> scene;
```

NB: `object` could be at any level in the hierarchy

### General case (A. and B.)

```mermaid
sequenceDiagram
  scene->>parent:vec.applyMatrix4(parent.matrixWorld^-1) OR* vec.applyMatrix4(parent.matrix^-1);
  parent->>scene:vec.applyMatrix4(parent.matrixWorld) OR* vec.applyMatrix4(parent.matrix);
  Note over scene,parent: *OR: because `parent` is direct child of the world: `parent.matrix` equals `parent.matrixWorld`

  parent->>child:vec.applyMatrix4(child.matrix^-1);
  child->>parent:vec.applyMatrix4(child.matrix);

  scene->>child:vec.applyMatrix4(child.matrixWorld^-1);
  child->>scene:vec.applyMatrix4(child.matrixWorld);
```

NB: 2 other sugar methods `Object3D.worldToLocal` and `Object3D.localToWorld` exist to jump from/to an object local/world space.

```mermaid
sequenceDiagram
  scene->>object:object.worldToLocal(vec);
  object->>scene:object.localToWorld(vec);
```

NB: It is equivalent to apply object's `.matrixWorld`/`.matrixWorld^-1`

## Camera - `Object3D.modelViewMatrix` shortcut

As already said, `Camera` is a regular `Object3d`: so it has a `.matrix` and a `.matrixWorld` property.

Plus conveniently, `Camera` also provides a pre-computed `.matrixWorldInverse` property (which is the same as `.matrixWorld.invert()` if we wanted to compute it manually)

---

That said, and according to the [`Matrix4` doc](https://threejs.org/docs/#api/en/math/Matrix4):
> An object's `modelViewMatrix` is the object's `matrixWorld` pre-multiplied by the camera's `matrixWorldInverse`.

Ie: `Object3D.modelViewMatrix = camera.matrixWorldInverse x Object3D.matrixWorld`

In other words, there exists a `Object3D.modelViewMatrix` property to create a shortcut to jump directly from object's local coordinates to camera coordinates by: `vec.applyMatrix4(object.modelViewMatrix)`

```mermaid
sequenceDiagram
  camera->>scene:vec.applyMatrix4(camera.matrixWorld);
  scene->>camera:vec.applyMatrix4(camera.matrixWorld^-1) OR* camera:vec.applyMatrix4(camera.matrixWorldInverse);
  Note over camera,scene: *OR: because of convenient `.matrixWorldInverse` property

  scene->>object:vec.applyMatrix4(object.matrixWorld^-1);
  object->>scene:vec.applyMatrix4(object.matrixWorld);


  object->>camera:vec.applyMatrix4(object.modelViewMatrix);
  camera->>object:vec.applyMatrix4(object.modelViewMatrix^-1);
```

## Screen

- [`Camera.projectionMatrix`](https://threejs.org/docs/index.html?q=matri#api/en/cameras/Camera.projectionMatrix) -- Camera space to NDC space
- `viewportMatrix` -- NDC space to screen space
- [`Vector3.project`](https://threejs.org/docs/index.html?q=vector#api/en/math/Vector3.project) ([source](https://github.com/mrdoob/three.js/blob/2a8a3faef5d2f8961026a23e1002d3f7d7ee5b87/src/math/Vector3.js#L315-L319)) -- world space to NDC space

```mermaid
sequenceDiagram
  screen->>ndc:vec.applyMatrix4(viewportMatrix^-1);
  ndc->>screen:vec.applyMatrix4(viewportMatrix);
  
  ndc->>camera:vec.applyMatrix4(camera.projectionMatrixInverse);
  camera->>ndc:vec.applyMatrix4(camera.projectionMatrix);

  #camera->>scene:vec.applyMatrix4(camera.matrixWorld);
  #scene->>camera:camera:vec.applyMatrix4(camera.matrixWorldInverse);

  ndc->>scene:vec.unproject(camera);
  scene->>ndc:vec.project(camera);
```

with:

```js
  const viewportMatrix = new THREE.Matrix4();
  const { x: WW, y: WH } = renderer.getSize(new THREE.Vector2()); // canvas size (dpr-independent)
  viewportMatrix.set(
    WW / 2, 0, 0, WW / 2,
    0, -WH / 2, 0, WH / 2,
    0, 0, 0.5, 0.5,
    0, 0, 0, 1
  );
  ```

## Summary

```mermaid
sequenceDiagram  
  participant ndc
  participant camera
  participant scene

  title: THREE spaces jumps

  camera->>ndc:vec.applyMatrix4(camera.projectionMatrix)

  scene->>ndc:vec.project(camera)

  camera->>scene:vec.applyMatrix4(camera.matrixWorld);

  object->>scene:vec.applyMatrix4(object.matrixWorld);

  object->>camera:vec.applyMatrix4(object.modelViewMatrix)
```

## _

<details>
  <!--
  Following is for https://abernier.github.io/abernier website
  -->
  
  <script type="module">
  import mermaid from 'https://unpkg.com/mermaid@9.2.2/dist/mermaid.esm.min.mjs';
  mermaid.init(undefined, '.language-mermaid');
  </script>
</details>


