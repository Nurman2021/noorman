<script>
  import { T, useFrame, useThrelte } from '@threlte/core'
  import Mecha from './models/Mecha.svelte'
  import { OrbitControls} from '@threlte/extras'

  export let moveCamera = {x,y,z};
  export let rotateCamera = {x,y,z};

</script>

<!-- <T.Group rotation.y={rotation}> -->
<T.Group>
  <T.PerspectiveCamera
    makeDefault
    position={[moveCamera.x, moveCamera.y, moveCamera.z]}
    fov={15}
    on:create={({ ref }) => {
      ref.lookAt(rotateCamera.x, rotateCamera.y, rotateCamera.z)
    }}
  >
      <OrbitControls

  />
  </T.PerspectiveCamera>
</T.Group>

  <Mecha/>

<!-- Floor -->
<T.Mesh  rotation.x={-Math.PI / 2} receiveShadow position.y={0.05}>
  <T.CircleGeometry args={[2, 40]} />
  <T.MeshStandardMaterial
    color="#cc4f55"
  />
</T.Mesh>

<T.Mesh  rotation.x={-Math.PI / 2} receiveShadow>
  <T.CircleGeometry args={[8, 40]} />
  <T.MeshStandardMaterial
    color="#252535"
  />
</T.Mesh>

<T.DirectionalLight
  position={[6, 6, 6]}
  intensity={1.7}
  castShadow
/>
<T.AmbientLight color="#ffffff" intensity={0.3}/>

<T.PointLight color="#ffffff" position={[0, 10, 10]}/>


