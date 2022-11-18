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
const scene = new THREE.Scene()                   // i)   Canvas <-> scene

const mesh = new THREE.Mesh()                     // ii)  element <-> Class
mesh.name = "myCube"                              // iii) prop <-> property
mesh.material = new THREE.meshNormalMaterial()    // i)   attach new object instance to .material
mesh.geometry = new THREE.BoxGeometry(1,1,1)      // v)   args <-> constructor (and implicit `attach="geometry"`)

const spotLight = new THREE.SpotLight()
spotLight.position.set(15, 15, 15)                // vi)  Vector3.set method
mesh.add(spotLight)                               // vii) .add() to parent (mesh)

scene.add(mesh)
```

- 1) https://docs.pmnd.rs/react-three-fiber/api/canvas
- 4) https://docs.pmnd.rs/react-three-fiber/api/objects#attach
- 5) https://docs.pmnd.rs/react-three-fiber/api/objects#constructor-arguments
- 6) https://docs.pmnd.rs/react-three-fiber/api/objects#set
