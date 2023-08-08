<script lang="ts">
	import { Capsule } from 'three/examples/jsm/math/Capsule';
	import { T, useFrame, useLoader } from '@threlte/core';
	import { onMount } from 'svelte';
	import { Clock, DirectionalLight, HemisphereLight, PerspectiveCamera, Vector3 } from 'three';
	import { Octree } from 'three/examples/jsm/math/Octree';
	import { TextureLoader } from 'three';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
	let gltfScene: any;
	// Init
	const texture = useLoader(TextureLoader).load('/assets/ground.jpeg');
	const GRAVITY = 5;
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

	const fillLight1 = new HemisphereLight(0x8dc1de, 0x00668d, 1.5);
	fillLight1.position.set(2, 1, 1);

	const directionalLight = new DirectionalLight(0xffffff, 2.5);
	directionalLight.position.set(-5, 25, -1);
	directionalLight.castShadow = true;
	directionalLight.shadow.camera.near = 0.01;
	directionalLight.shadow.camera.far = 500;
	directionalLight.shadow.camera.right = 30;
	directionalLight.shadow.camera.left = -30;
	directionalLight.shadow.camera.top = 30;
	directionalLight.shadow.camera.bottom = -30;
	directionalLight.shadow.mapSize.width = 1024;
	directionalLight.shadow.mapSize.height = 1024;
	directionalLight.shadow.radius = 4;
	directionalLight.shadow.bias = -0.00006;

	// Init Octree (colisions)
	const worldOctree = new Octree();
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
			damping *= 0.2;
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
				playerVelocity.y = 10;
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
		const loader = new GLTFLoader().setPath('/assets/');

		loader.load('collision-world.glb', (gltf) => {
			gltf.scene.scale.set(2, 2, 2);
			gltf.scene.position.set(0, 2, 0);
			gltfScene = gltf;
			worldOctree.fromGraphNode(gltfScene.scene);
		});
	});
</script>

{#if gltfScene}
	<T is={gltfScene.scene} />
{/if}
<T is={camera} makeDefault />

<!-- Lights -->
<T is={fillLight1} />
<T is={directionalLight} />
