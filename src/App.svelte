<script>
  import { CircleBufferGeometry, MeshStandardMaterial, BoxBufferGeometry, DoubleSide } from "three";
  import { DEG2RAD } from "three/src/math/MathUtils";
  import { AmbientLight, Canvas, DirectionalLight, Group, HemisphereLight, Mesh, OrbitControls, PerspectiveCamera } from "@threlte/core";
  import { spring } from "svelte/motion";
  import { GLTF } from "@threlte/extras";

  const scale = spring(1);
</script>

<main>
  <Canvas size={{ height: 1000, width: 1000 }}>
    <PerspectiveCamera position={{ x: 10, y: 10, z: 10 }} fov={24}>
      <OrbitControls autoRotate={false} enableZoom={true} target={{ y: 0.5 }} />
    </PerspectiveCamera>

    <DirectionalLight shadow position={{ x: 3, y: 10, z: 10 }} />
    <DirectionalLight position={{ x: -3, y: 10, z: -10 }} intensity={0.2} />
    <AmbientLight intensity={0.2} />

    <Group scale={$scale}>
      <GLTF castShadow receiveShadow url={"https://decoded-files.fra1.digitaloceanspaces.com/deadly-games/deadly.glb"} position={{ y: 1 }} scale={3} />
    </Group>

    <!-- Floor -->
    <Mesh
      receiveShadow
      rotation={{ x: -90 * (Math.PI / 180) }}
      geometry={new CircleBufferGeometry(3, 72)}
      material={new MeshStandardMaterial({ side: DoubleSide, color: "white" })}
    />
  </Canvas>
</main>

<style>
  main {
    height: 100vh;
    width: 100vw;
  }
</style>
