<script lang="ts">
	import { T, useThrelte } from '@threlte/core';
	import { ContactShadows, Grid, HTML } from '@threlte/extras';
	import Player from './Player.svelte';
	import { ACESFilmicToneMapping, Color, Fog, VSMShadowMap } from 'three';

	const { scene, renderer } = useThrelte();
	scene.background = new Color('black');
	scene.fog = new Fog(0x88ccee, 0, 50);
	renderer.shadowMap.enabled = true;
	renderer.shadowMap.type = VSMShadowMap;
	renderer.toneMapping = ACESFilmicToneMapping;
</script>

<Player />

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
		<p>Text</p>
	</HTML>
</T.Mesh>

<style>
	p {
		color: white;
	}
</style>
