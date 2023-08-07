<script lang="ts">
	import { Canvas } from '@threlte/core';
	import { World } from '@threlte/rapier';
	import Scene from './Scene.svelte';
	import FallbackScene from './FallbackScene.svelte';
</script>

<Canvas>
	<World>
		<Scene />
		<FallbackScene slot="fallback" />
	</World>
</Canvas>
