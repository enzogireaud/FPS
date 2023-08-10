<script lang="ts">
	import { T } from '@threlte/core';
	import { onMount } from 'svelte';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
	import type { Octree } from 'three/examples/jsm/math/Octree';
	export let worldOctree: Octree;
	export let moonOctree: Octree;
	let gltfScene: any;
	let gltfMoon: any;
	onMount(() => {
		const loader = new GLTFLoader().setPath('/assets/');
		loader.load('map.glb', (gltf) => {
			gltf.scene.scale.set(0.1, 0.1, 0.1);
			gltf.scene.position.set(0, -2, 0);
			gltfScene = gltf;
			worldOctree.fromGraphNode(gltfScene.scene);
		});
		loader.load('moon.glb', (gltf) => {
			gltf.scene.scale.set(10, 10, 10);
			gltf.scene.position.set(0, 100, -100);
			gltfMoon = gltf;
			moonOctree.fromGraphNode(gltfMoon.scene);
		});
	});
</script>

{#if gltfScene}
	<T is={gltfScene.scene} />
{/if}
{#if gltfMoon}
	<T is={gltfMoon.scene} />
{/if}
