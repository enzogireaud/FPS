<script lang="ts">
	import { T, useThrelte } from '@threlte/core';
	import { ContactShadows, Grid, HTML } from '@threlte/extras';
	import Player from './Player.svelte';
	import { ACESFilmicToneMapping, Color, Fog, VSMShadowMap } from 'three';

	const { scene, renderer } = useThrelte();
	scene.background = new Color('black');
	scene.fog = new Fog('#b5bbc4', 0, 100);
	renderer.shadowMap.enabled = true;
	renderer.shadowMap.type = VSMShadowMap;
	renderer.toneMapping = ACESFilmicToneMapping;
</script>

<Player />

<T.DirectionalLight intensity={0.8} position.x={5} position.y={10} />
<T.AmbientLight intensity={0.2} />

<ContactShadows scale={10} blur={2} far={2.5} opacity={0.5} />

<T.Mesh>
	<HTML transform position.y={3}>
		<p>3d scene coming soon</p>
	</HTML>
</T.Mesh>

<style>
	p {
		color: white;
	}
</style>
