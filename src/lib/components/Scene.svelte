<script lang="ts">
	import { useThrelte } from '@threlte/core';
	import { EquirectangularReflectionMapping, Fog, TextureLoader } from 'three';
	import World from './World.svelte';
	import { onMount } from 'svelte';
	import Lights from './Lights.svelte';

	// scene global settings
	const { scene } = useThrelte();
	// scene.fog = new Fog('#b5bbc4', 0, 150);
	const textureLoader = new TextureLoader();

	// Load envmap
	onMount(() => {
		const environmentMap = textureLoader.load('/assets/envmap.jpg');
		environmentMap.mapping = EquirectangularReflectionMapping;
		scene.background = environmentMap;
		scene.environment = environmentMap;
	});
</script>

<Lights />
<World />
