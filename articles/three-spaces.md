---
tags: three, maths
---

THREE spaces
===

Related article: [Change of basis](change-of-basis.md)

## "Space"

In a 3d environment, a "space" is defined by 3 perpendicular axes and an origin:
[![image](https://docs.google.com/drawings/d/e/2PACX-1vSgAWjz37yphQgR4NkrW8Lehtg3LAVJmqn0fMcGoMgxETzU1dj_V36MAPUfCwSFrtRkK3IEVsMzeWXE/pub?w=1440&h=1080)](https://docs.google.com/drawings/d/1thD_hICjAhERuMXal7ez8HhEk9JPYV7iF5Mhb2VIYCU/edit)

It allows us to locate any "point" in this space, by its 3 coordinates components:
[![image](https://docs.google.com/drawings/d/e/2PACX-1vQDwhYakAtd5OudiUgRExULy6C-LwD-KTuOe_k0DpkR3nAGWkj4LXOdYEt8f3rKgb7g3EA10YGq7EQu/pub?w=1440&h=1080)](https://docs.google.com/drawings/d/1kflQFDXOeOiT6VvMu_0fktmethV-jprDh9zLv8ZBshE/edit)

**In THREE, a such space is called an [`Object3D`](https://threejs.org/docs/index.html?q=object#api/en/core/Object3D).**

## World space

A default "world" space will always exist, the [`Scene`](https://threejs.org/docs/index.html?q=scene#api/en/scenes/Scene):
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

Learn more about Three [matrices](https://threejs.org/docs/#manual/en/introduction/Matrix-transformations).

## Change of basis

Sometimes, we know the coordinates of a point in a given "space", but we want to express them in another "space"... That is what we call a [change of basis](change-of-basis.md).

We represent this point by a `vec`tor.

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

> **Warning** <br>
> 
> `.matrixWorldInverse` only [exists on cameras](https://threejs.org/docs/#api/en/math/Matrix4) => `matrixWorld^-1` has to be computed for any other object3d.

> **Note**<br>`.updateMatrixWorld()`
>
> In Three.js, every object in the scene graph (which is a hierarchical tree structure of objects) has two main matrices:
> 
> - `matrix`: This is the local transformation matrix of the object relative to its parent.
> - `matrixWorld`: This represents the transformation of the object in world coordinates, i.e., relative to the root of the scene graph.
> 
> When you make changes to the position, rotation, or scale of an object, its local transformation matrix (`matrix`) is updated. However, the world matrix (`matrixWorld`) is not automatically updated for performance reasons. If you want the changes to be reflected in the world matrix, you need to call `.updateMatrixWorld()`.
> 
> Here's an example of how you might use it:
> 
> ```js
> let mesh = new THREE.Mesh(geometry, material);
> scene.add(mesh);
> 
> // Change the position of the mesh
> mesh.position.x = 10;
> 
> // Now we need to update the world matrix for these changes to take effect
> mesh.updateWorldMatrix(true, true);  // The first 'true' updates the ancestors, and the second 'true' updates the descendants
> ```
> 
> In this example, `mesh.updateWorldMatrix(true, true)` will update the world matrix of the mesh, its ancestors, and its descendants, ensuring that all transformations are correctly applied.

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

NB: 2 other sugar methods [`Object3D.worldToLocal`](https://threejs.org/docs/#api/en/core/Object3D.worldToLocal) and [`Object3D.localToWorld`](https://threejs.org/docs/#api/en/core/Object3D.localToWorld) exist to jump from/to an object local/world space.

```mermaid
sequenceDiagram
  scene->>object:object.worldToLocal(vec);
  object->>scene:object.localToWorld(vec);
```

It is equivalent to apply object's `.matrixWorld`/`.matrixWorld^-1`

NB: There also exist [`Object3d.getWorldPosition`](https://threejs.org/docs/#api/en/core/Object3D.getWorldPosition) which simply returns the origin (0,0,0) of the object in world coordinates. 

## Camera - `Object3D.modelViewMatrix` shortcut

[![](https://docs.google.com/drawings/d/e/2PACX-1vTQP13uFWiqM3IX8ZZILz5SW3kwg8r9DkUV1OwzvR7QcU34y_1heZqJJVDyFsqv-ipHs5TXcXvkc071/pub?w=1440&h=1080)](https://docs.google.com/drawings/d/1eJa-7MOPR5sXypAX5hTJkegBwtLOnBdLGjKCacWR7Sw/edit)

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

[![](https://docs.google.com/drawings/d/e/2PACX-1vS8XHBK6UUptS62Jkv8O4SgfdQ-x9KWSAMMbVSwR8GDFatS23k_ozwa6BPz-c_VI1m4Mzawkqsu94aM/pub?w=1440&h=1080)](https://docs.google.com/drawings/d/1RI6tYwoHcmJ5nASYFtumOS9CSJUysieyRznOsQ5f4t4/edit)

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
---
title: THREE spaces jumps
---

sequenceDiagram  
  participant ndc
  participant camera
  participant scene

  camera->>ndc:vec.applyMatrix4(camera.projectionMatrix)

  scene->>ndc:vec.project(camera)

  camera->>scene:vec.applyMatrix4(camera.matrixWorld);

  object->>scene:vec.applyMatrix4(object.matrixWorld);

  object->>camera:vec.applyMatrix4(object.modelViewMatrix)
```
