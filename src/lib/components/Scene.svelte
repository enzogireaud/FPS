<script lang="ts">
	import { T, useThrelte } from '@threlte/core';
	import { ContactShadows, HTML } from '@threlte/extras';
	import { EquirectangularReflectionMapping, Fog, MeshBasicMaterial, TextureLoader } from 'three';
	import World from './World.svelte';
	import { onMount } from 'svelte';

	// scene global settings
	const { scene } = useThrelte();
	scene.fog = new Fog('#b5bbc4', 0, 150);

	const textureLoader = new TextureLoader();

	// Load envmap
	onMount(() => {
		const environmentMap = textureLoader.load('/assets/envmap.jpg');
		environmentMap.mapping = EquirectangularReflectionMapping;
		scene.background = environmentMap;
		scene.environment = environmentMap;
	});
</script>

<World />

<T.DirectionalLight intensity={0.8} position.x={5} position.y={10} />
<T.AmbientLight intensity={0.2} />

<ContactShadows scale={10} blur={2} far={2.5} opacity={0.5} />

<T.Mesh position={[10, -2, -6]}>
	<HTML transform position.y={3} rotation.y={Math.PI / 2}>
		<button>
			<i class="fa-solid fa-xmark" />
		</button>
	</HTML>
	<T.MeshBasicMaterial />
</T.Mesh>

<style>
	i {
		color: red;
		font-size: 5em;
	}
</style>
