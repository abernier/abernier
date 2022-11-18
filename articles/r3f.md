r3f : THREE
===

r3f
---

```jsx
<Canvas>
  <mesh name="myCube">
    <meshNormalMaterial attach="material" />
    <boxGeometry args={[1, 1, 1]} />
	
    <spotLight position={[15, 15, 15]} />
  </mesh>
</Canvas>
```

THREE
---

```js
const scene = new THREE.Scene() // [^1] Canvas <-> scene

const mesh = new THREE.Mesh() // element <-> Class
mesh.name = "myCube" // prop <-> property
mesh.material = new THREE.meshNormalMaterial() // attach instance to .material
mesh.geometry = new THREE.BoxGeometry(1,1,1) // args <-> constructor

const spotLight = new THREE.SpotLight()
spotLight.position.set(15, 15, 15) // Vector3.set method
mesh.add(spotLight) // .add() to parent (mesh)

scene.add(mesh) // .add to parent (scene)
```
