<script lang="ts">
	import { T, useFrame } from '@threlte/core';
	import { onMount } from 'svelte';
	import { Clock, PerspectiveCamera, Vector3 } from 'three';
	import { Capsule } from 'three/examples/jsm/math/Capsule';
	import type { Octree } from 'three/examples/jsm/math/Octree';

	// Init
	const GRAVITY = 3;
	const STEPS_PER_FRAME = 5;
	// const FLOOR: Mesh = new Mesh();
	// const FLOOR2: Mesh = new Mesh();
	const clock = new Clock();
	let camera: PerspectiveCamera = new PerspectiveCamera(
		70,
		window.innerWidth / window.innerHeight,
		0.1,
		1000
	);
	camera.rotation.order = 'YXZ';

	// Init Octree (colisions)
	export let worldOctree: Octree;
	const playerCollider = new Capsule(new Vector3(0, 0.35, 0), new Vector3(0, 1, 0), 0.35);
	const playerVelocity = new Vector3();
	const playerDirection = new Vector3();
	let playerOnFloor = false;
	let mouseTime = 0;

	// Définir le type de keyStates
	interface KeyStates {
		[key: string]: boolean;
	}
	const keyStates: KeyStates = {};

	// Main functions

	// Gère la collision avec le world
	function playerCollisions() {
		const result = worldOctree.capsuleIntersect(playerCollider);

		playerOnFloor = false;

		if (result) {
			playerOnFloor = result.normal.y > 0;

			if (!playerOnFloor) {
				playerVelocity.addScaledVector(result.normal, -result.normal.dot(playerVelocity));
			}

			playerCollider.translate(result.normal.multiplyScalar(result.depth));
		}
	}

	// Fonction pour update la pos du player
	function updatePlayer(deltaTime: number) {
		let damping = Math.exp(-4 * deltaTime) - 1;

		if (!playerOnFloor) {
			playerVelocity.y -= GRAVITY * deltaTime;
			// small air resistance
			damping *= 0.3;
		}

		playerVelocity.addScaledVector(playerVelocity, damping);

		const deltaPosition = playerVelocity.clone().multiplyScalar(deltaTime);
		playerCollider.translate(deltaPosition);

		playerCollisions();

		camera.position.copy(playerCollider.end);
		camera.position.y = playerCollider.end.y + 1;
	}

	// Choper le vecteur avant arriere
	function getForwardVector() {
		camera.getWorldDirection(playerDirection);
		playerDirection.y = 0;
		playerDirection.normalize();

		return playerDirection;
	}

	// Vecteur cotés
	function getSideVector() {
		camera.getWorldDirection(playerDirection);
		playerDirection.y = 0;
		playerDirection.normalize();
		playerDirection.cross(camera.up);

		return playerDirection;
	}

	// On bind les f sur les touches
	function controls(deltaTime: number) {
		// gives a bit of air control
		const speedDelta = deltaTime * (playerOnFloor ? 25 : 8);

		if (keyStates['KeyW']) {
			playerVelocity.add(getForwardVector().multiplyScalar(speedDelta * 0.5));
		}

		if (keyStates['KeyS']) {
			playerVelocity.add(getForwardVector().multiplyScalar(-speedDelta * 0.5));
		}

		if (keyStates['KeyA']) {
			playerVelocity.add(getSideVector().multiplyScalar(-speedDelta * 0.5));
		}

		if (keyStates['KeyD']) {
			playerVelocity.add(getSideVector().multiplyScalar(speedDelta * 0.5));
		}

		if (playerOnFloor) {
			if (keyStates['Space']) {
				playerVelocity.y = 8;
			}
		}
	}
	// Si le player tombe
	function teleportPlayerIfOob() {
		if (camera.position.y <= -25) {
			playerCollider.start.set(0, 0.35, 0);
			playerCollider.end.set(0, 1, 0);
			playerCollider.radius = 0.35;
			camera.position.copy(playerCollider.end);
			camera.rotation.set(0, 0, 0);
		}
	}

	// Tick
	useFrame(() => {
		const deltaTime = Math.min(0.05, clock.getDelta());
		for (let i = 0; i < STEPS_PER_FRAME; i++) {
			controls(deltaTime);

			updatePlayer(deltaTime);

			teleportPlayerIfOob();
		}
	});

	// On recupère les eventListener après le mount du composant pour etre sur d'avoir les élements du dom a dispo
	onMount(() => {
		document.addEventListener('keydown', (event) => {
			keyStates[event.code] = true;
		});

		document.addEventListener('keyup', (event) => {
			keyStates[event.code] = false;
		});

		addEventListener('mousedown', () => {
			document.body.requestPointerLock();

			mouseTime = performance.now();
		});

		document.body.addEventListener('mousemove', (event) => {
			if (document.pointerLockElement === document.body) {
				camera.rotation.y -= event.movementX / 500;
				camera.rotation.x -= event.movementY / 500;
			}
		});
	});
</script>

<T is={camera} makeDefault />