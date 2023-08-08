<script lang="ts">
	import { T } from '@threlte/core';
	import { onMount } from 'svelte';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
	import type { Octree } from 'three/examples/jsm/math/Octree';
	export let worldOctree: Octree;
	let gltfScene: any;
	onMount(() => {
		const loader = new GLTFLoader().setPath('/assets/');
		loader.load('map.glb', (gltf) => {
			gltf.scene.scale.set(0.01, 0.01, 0.01);
			gltf.scene.position.set(0, -0.2, 0);
			gltfScene = gltf;
			worldOctree.fromGraphNode(gltfScene.scene);
		});
	});
</script>

{#if gltfScene}
	<T is={gltfScene.scene} />
{/if}
