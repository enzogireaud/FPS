<script lang="ts">
	import { T, useFrame } from '@threlte/core';
	import { HTML } from '@threlte/extras';
	import type { Mesh } from 'three';

	// Attributs
	export let text: string;
	export let button: Mesh;
	export let isHovered: Boolean;
	$: color = isHovered ? 'white' : 'black';
	let title: HTMLElement;

	useFrame(() => {
		title.style.color = color;
	});
</script>

<T is={button} position={[-12.5, -0.5, -6.7]} rotation.y={Math.PI / 2}>
	<T.PlaneGeometry args={[4, 2, 1]} />
	<T.MeshStandardMaterial transparent opacity={0} />

	<HTML transform occlude position.z={0.2} scale={0.5}>
		<p bind:this={title}>{text}</p>
	</HTML>
</T>

<style>
	p {
		font-size: 0.1em;
		max-width: 300px;
		text-align: center;
	}
</style>
