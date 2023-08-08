<script lang="ts">
	import { T } from '@threlte/core';
	import { ContactShadows } from '@threlte/extras';
	import { DirectionalLight, HemisphereLight } from 'three';

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
</script>

<T is={fillLight1} />
<T is={directionalLight} />

<T.DirectionalLight intensity={0.8} position.x={5} position.y={10} />
<T.AmbientLight intensity={0.2} />

<ContactShadows scale={10} blur={2} far={2.5} opacity={0.5} />
