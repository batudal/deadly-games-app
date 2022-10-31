<script lang="ts">
  import { CircleBufferGeometry, MeshStandardMaterial, BoxBufferGeometry, DoubleSide } from "three";
  import { DEG2RAD } from "three/src/math/MathUtils";
  import { PositionalAudio, useAudioListener, Audio, PositionalAudioHelper, AudioListener } from "@threlte/core";
  import { HTML } from "@threlte/extras";

  import {
    AmbientLight,
    Pass,
    SpotLight,
    PointLight,
    Canvas,
    DirectionalLight,
    Group,
    HemisphereLight,
    Mesh,
    OrbitControls,
    PerspectiveCamera,
  } from "@threlte/core";
  import { GlitchPass } from "three/examples/jsm/postprocessing/GlitchPass";
  import { FilmPass } from "three/examples/jsm/postprocessing/FilmPass";
  import { DotScreenPass } from "three/examples/jsm/postprocessing/DotScreenPass";

  import { spring } from "svelte/motion";
  import { GLTF, useGltf } from "@threlte/extras";
  import { onMount } from "svelte";
  const { gltf } = useGltf("https://decoded-files.fra1.digitaloceanspaces.com/deadly-games/deadly.glb");
  import { Fog } from "@threlte/core";
  import { useProgress } from "@threlte/extras";
  import { tweened } from "svelte/motion";
  import { fade } from "svelte/transition";

  $: if ($gltf) {
    console.log($gltf);
  }
  const scale = spring(1);
  const { progress } = useProgress();

  const tweenedProgress = tweened($progress, {
    duration: 800,
  });
  $: tweenedProgress.set($progress);
  let myx = 0;
  let myz = 0;
  let counter = 0;
  let streetLight = true;
  let music = false;
  onMount(() => {
    setInterval(() => {
      myx = Math.sin(counter) * 40;
      myz = Math.cos(counter) * 40;
      counter += 0.1;
      let random = Math.random() * 100;
      if (random < 5) {
        streetLight = !streetLight;
      }
    }, 50);
  });
</script>

<main>
  {#if $tweenedProgress < 1}
    <div
      transition:fade|local={{
        duration: 200,
      }}
      class="wrapper"
    >
      <p class="loading">Loading</p>
      <div class="bar-wrapper">
        <div class="bar" style="width: {$tweenedProgress * 100}%" />
      </div>
    </div>
  {/if}
  <Canvas size={{ height: window.innerHeight, width: window.innerWidth }}>
    <Fog color={"#444444"} near={25} far={100} />
    <HTML occlude transform distanceFactor={1.17} position={{ x: -19.6, y: 28, z: -8.25 }} rotation={{ y: -Math.PI / 2 }}>
      <img
        src="./invis.gif"
        alt=""
        on:click={() => {
          music = true;
        }}
        on:keydown
      />
    </HTML>
    <HemisphereLight intensity={0.5} skyColor={0xffbd08} groundColor={0x323973} />

    <!-- <Pass pass={new FilmPass(1000, 0, 0, false)} /> -->
    <!-- <PointLight visible position={{ x: 30, y: 3, z: 10 }} color={"#44F1A6"} /> -->
    <PointLight
      rotation={{ z: -Math.PI / 2 }}
      power={0.1}
      receiveShadow
      shadow
      decay={4}
      visible
      position={{ x: -3, y: 21, z: 3 }}
      color={"#44F1A6"}
      intensity={5}
    />

    {#if streetLight}
      <PointLight
        rotation={{ z: -Math.PI / 2 }}
        power={0.1}
        receiveShadow
        shadow
        decay={4}
        visible
        position={{ x: 25, y: 21, z: 10 }}
        color={"#C27500"}
        intensity={5}
      />
    {/if}

    <PerspectiveCamera fov={60} position={{ x: -60, y: 20, z: 60 }} lookAt={{ x: 0, y: 0, z: 0 }}>
      <OrbitControls autoRotate={false} enableZoom={true} />
      <AudioListener />
    </PerspectiveCamera>

    <AmbientLight intensity={0.1} />
    <Group scale={$scale}>
      <GLTF castShadow receiveShadow url={"https://decoded-files.fra1.digitaloceanspaces.com/deadly-games/deadly.glb"} scale={3} />
      {#if music}
        <PositionalAudio
          refDistance={10}
          volume={1}
          loop
          autoplay
          playbackRate="1"
          directionalCone={{
            coneInnerAngle: 90,
            coneOuterAngle: 220,
            coneOuterGain: 0.3,
          }}
          source={"/dilla.mp3"}
          position={{ x: -15, y: 25, z: 25 }}
        >
          <!-- <PositionalAudioHelper /> -->
        </PositionalAudio>
      {/if}
    </Group>
  </Canvas>
</main>

<style>
  main {
    height: 100vh;
    width: 100vw;
  }
  .wrapper {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    background-color: white;
    display: flex;
    flex-direction: column;
    gap: 0.25rem;
    align-items: center;
    justify-content: center;
    color: black;
  }

  .loading {
    font-size: 0.875rem;
    line-height: 1.25rem;
  }

  .bar-wrapper {
    width: 33.333333%;
    height: 10px;
    border: 1px solid black;
    position: relative;
  }

  .bar {
    height: 100%;
    background-color: black;
  }
  img {
    width: 1600px;
    height: 1600px;
  }
</style>
