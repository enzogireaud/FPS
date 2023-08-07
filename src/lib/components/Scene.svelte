<script lang="ts">
	import { T } from '@threlte/core';
	import { ContactShadows, Grid, HTML } from '@threlte/extras';
	import { onMount } from 'svelte';

	let cameraX: number = 0;
	let cameraY: number = 1;
	let cameraZ: number = 10;
	let cameraRotationX: number = 0;
	let cameraRotationY: number = 0;

	function setupFirstPersonControls() {
		// Gérer les mouvements de la caméra pour la vue FPS
		const moveSpeed = 0.3;
		const rotateSpeed = 0.002;

		const onKeyDown = (event: KeyboardEvent) => {
			switch (event.key) {
				case 'z':
					cameraZ += -moveSpeed;
					break;
				case 's':
					cameraZ += moveSpeed;
					break;
				case 'q':
					cameraX += -moveSpeed;
					break;
				case 'd':
					cameraX += moveSpeed;
					break;
			}
		};

		const onMouseMove = (event: MouseEvent) => {
			const deltaX = event.movementX;
			const deltaY = event.movementY;

			cameraRotationY -= deltaX * rotateSpeed;
			cameraRotationX -= deltaY * rotateSpeed;

			// Limiter l'angle vertical de la caméra pour éviter le retournement
			cameraRotationX = Math.max(-Math.PI / 2, Math.min(Math.PI / 2, cameraRotationX));
		};

		window.addEventListener('keydown', onKeyDown);
		window.addEventListener('mousemove', onMouseMove);
	}

	onMount(() => {
		setupFirstPersonControls();
	});
</script>

<T.PerspectiveCamera
	makeDefault
	position={[cameraX, cameraY, cameraZ]}
	on:create={({ ref }) => {
		ref.lookAt(0, 3, 0);
	}}
	rotation.x={cameraRotationX}
	rotation.y={cameraRotationY}
/>

<T.DirectionalLight intensity={0.8} position.x={5} position.y={10} />
<T.AmbientLight intensity={0.2} />

<Grid
	position.y={-0.001}
	cellColor="#ffffff"
	sectionColor="#ffffff"
	sectionThickness={0}
	fadeDistance={25}
	cellSize={2}
/>

<ContactShadows scale={10} blur={2} far={2.5} opacity={0.5} />

<T.Mesh>
	<HTML transform position.y={3}>
		<p>Prout en 3D lol</p>
	</HTML>
</T.Mesh>

<style>
	p {
		color: white;
	}
</style>
