<script lang="ts">
	import { T, useFrame } from '@threlte/core';
	import { onMount } from 'svelte';
	import { Mesh, ShaderMaterial, TextureLoader } from 'three';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
	import type { Octree } from 'three/examples/jsm/math/Octree';
	export let worldOctree: Octree;
	export let moonOctree: Octree;
	let gltfScene: any;
	let gltfMoon: any;
	const material = new ShaderMaterial({
		transparent: true,
		uniforms: {
			uTime: { value: 0 }
		},
		vertexShader: `
		varying vec2 vUv;
			void main()
			{
				vUv= uv;
				gl_Position = projectionMatrix * modelViewMatrix * vec4(position,1.0);
			}
			`,
		fragmentShader: `
		varying vec2 vUv;
		uniform float uTime;




vec3 palette( float t) {
    vec3 a = vec3(0.5,0.5,0.5);
    vec3 b = vec3(0.5,0.5,0.5);
    vec3 c = vec3(1.0,1.0,1.0);
    vec3 d = vec3(0.263,0.416,0.557);




    return a + b*cos( 6.28318*(c*t*d));
}
			void main()
			{
				// Aspect ratio
                vec2 uv = vUv * 2.0 - 1.0;
                vec2 uv0 = uv;

                vec3 finalColor = vec3(0.0);

                // LOOP

                for (float i = 0.0; i < 6.0 ; i++) {
                uv = fract(uv * 1.5) - 0.5;

                float d = length(uv) * exp(-length(uv0) );

                vec3 col = palette(length(uv0) + i *.4 + uTime * .5);

                d = sin(d * 15. + uTime) / 5.;
                d = abs(d);

                d = pow(0.01 / d, 1.5);

                finalColor += col * d;
             
            }
            gl_FragColor = vec4(finalColor,0.2);
			}
			`
	});
	onMount(() => {
		const loader = new GLTFLoader().setPath('/assets/');
		loader.load('map.glb', (gltf) => {
			// gltf.scene.traverse((child) => {
			// 	if (child instanceof Mesh) {
			// 		child.material = material;
			// 	}
			// });
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
	useFrame((delta) => {
		material.uniforms.uTime.value = delta.clock.elapsedTime;
	});
</script>

{#if gltfScene}
	<T is={gltfScene.scene} />
{/if}
{#if gltfMoon}
	<T is={gltfMoon.scene} />
{/if}
