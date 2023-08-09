<script lang="ts">
	import { Octree } from 'three/examples/jsm/math/Octree';
	import Map from './Map.svelte';
	import Player from './Player.svelte';
	import Text from './Text.svelte';
	import { useFrame } from '@threlte/core';
	import { Mesh, PerspectiveCamera, Raycaster, Vector2 } from 'three';
	import { onMount } from 'svelte';

	// Init Octree (colisions)
	const worldOctree = new Octree();
	const camera: PerspectiveCamera = new PerspectiveCamera(
		70,
		window.innerWidth / window.innerHeight,
		0.1,
		1000
	);

	//
	// Raycaster
	let button: Mesh = new Mesh();
	let isHovered: Boolean = false;
	let text: string = 'Hello world';
	let raycaster = new Raycaster();
	let intersection;
	function doSomethingWithRaycaster() {
		text =
			"Hello i'm Enzo, a freelance creative dev Lorem ipsum dolor sit amet consectetur adipisicing elit. Totam asperiores suscipit illum ea id aut quod possimus consectetur alias mollitia. Repellendus ut voluptas molestias nam exercitationem rem necessitatibus placeat, hic possimus, beatae omnis. Impedit nostrum ab dolore, quo laudantium ipsa ";
	}
	useFrame(() => {
		// Raycast
		raycaster.setFromCamera(new Vector2(), camera);
		intersection = raycaster.intersectObject(button);
		if (intersection.length > 0) {
			isHovered = true;
		} else {
			isHovered = false;
		}
	});
	onMount(() => {
		addEventListener('click', () => {
			if (isHovered) {
				doSomethingWithRaycaster();
			}
		});
	});
</script>

<Map {worldOctree} />
<Player {worldOctree} {camera} />
<Text {button} {text} {isHovered} />
