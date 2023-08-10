<script lang="ts">
	import { Canvas } from '@threlte/core';
	import { useProgress } from '@threlte/extras';
	import { tweened } from 'svelte/motion';
	import { fade } from 'svelte/transition';
	import Scene from './Scene.svelte';
	const { progress } = useProgress();
	const tweenedProgress = tweened($progress, {
		duration: 1000
	});
	$: tweenedProgress.set($progress);
</script>

{#if $tweenedProgress < 1}
	<div
		transition:fade|local={{
			duration: 800
		}}
		class="wrapper"
	>
		<p class="loading">Loading</p>
		<div class="bar-wrapper">
			<div class="bar" style="width: {$tweenedProgress * 100}%" />
			<p>{Math.round($tweenedProgress * 100)} %</p>
		</div>
	</div>
{/if}

<Canvas>
	<Scene />
</Canvas>

<style lang="scss">
	.wrapper {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		left: 0;
		background-color: black;
		display: flex;
		flex-direction: column;
		gap: 0.25rem;
		align-items: center;
		justify-content: center;
		color: white;
	}
	.loading {
		font-size: 0.875rem;
		line-height: 1.25rem;
	}
	.bar-wrapper {
		width: 33.333333%;
		height: 10px;
		border: 1px solid white;
		position: relative;
		text-align: center;
	}
	.bar {
		height: 100%;
		background-color: white;
	}
</style>
