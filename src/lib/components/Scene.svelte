<script lang="ts">
	import { useThrelte } from '@threlte/core';
	import { Color, EquirectangularReflectionMapping, TextureLoader } from 'three';
	import World from './World.svelte';
	import { onMount } from 'svelte';
	import Lights from './Lights.svelte';

	// scene global settings
	const { scene } = useThrelte();
	const textureLoader = new TextureLoader();

	// Load envmap
	onMount(() => {
		const environmentMap = textureLoader.load('/assets/envmaps/envmap.jpg');
		environmentMap.mapping = EquirectangularReflectionMapping;
		scene.background = environmentMap;
		scene.environment = environmentMap;
	});
</script>

<Lights />
<World />
