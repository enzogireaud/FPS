<script lang="ts">
	import { T, useLoader } from '@threlte/core';
	import { onMount } from 'svelte';
	import { DoubleSide, Mesh, TextureLoader } from 'three';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
	import type { Octree } from 'three/examples/jsm/math/Octree';
	export let worldOctree: Octree;
	export let moonOctree: Octree;
	let gltfMoon: any;
	let floor: Mesh = new Mesh();
	onMount(() => {
		const loader = new GLTFLoader().setPath('/assets/');
		loader.load('moon.glb', (gltf) => {
			gltf.scene.scale.set(10, 10, 10);
			gltf.scene.position.set(0, 100, -100);
			gltfMoon = gltf;
			moonOctree.fromGraphNode(gltfMoon.scene);
		});
		worldOctree.fromGraphNode(floor);
	});
	const texture = useLoader(TextureLoader).load('/assets/texture.jpeg');
</script>

{#if floor}
	<T is={floor} rotation.x={-Math.PI * 0.5} position.y={-10}>
		<T.PlaneGeometry args={[100, 100, 100]} />
		{#await texture then value}
			<T.MeshPhongMaterial side={DoubleSide} map={value} shininess={0} />
		{/await}
	</T>
{/if}
{#if gltfMoon}
	<T is={gltfMoon.scene} />
{/if}
