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
const scene = new THREE.Scene()                   // 1. Canvas <-> scene

const mesh = new THREE.Mesh()                     // 2. element <-> Class
mesh.name = "myCube"                              // 3. prop <-> property
mesh.material = new THREE.meshNormalMaterial()    // 4. attach instance to .material
mesh.geometry = new THREE.BoxGeometry(1,1,1)      // 5. args <-> constructor

const spotLight = new THREE.SpotLight()
spotLight.position.set(15, 15, 15)                // 6. Vector3.set method
mesh.add(spotLight)                               // 7. .add() to parent (mesh)

scene.add(mesh)
```

- 4: https://docs.pmnd.rs/react-three-fiber/api/objects#attach
- 7: https://docs.pmnd.rs/react-three-fiber/api/objects#set
