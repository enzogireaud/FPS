<script lang="ts">
	import App from '$lib/components/App.svelte';
	import '../style.css';
</script>

<svelte:head>
	<link
		rel="stylesheet"
		href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.2/css/all.min.css"
		integrity="sha512-z3gLpd7yknf1YoNbCzqRKc4qyor8gaKU1qmn+CShxbuBusANI9QpRohGBreCFkKxLhei6S9CQXFEbbKuqLg0DA=="
		crossorigin="anonymous"
		referrerpolicy="no-referrer"
	/>
</svelte:head>
<div>
	<i class="fa-solid fa-xmark" id="cursor" />
	<App />
</div>

<style lang="scss">
	:global(body) {
		margin: 0;
	}

	div {
		position: relative;
		width: 100vw;
		height: 100vh;
		background: rgb(13, 19, 32);
		background: linear-gradient(180deg, rgba(13, 19, 32, 1) 0%, rgba(8, 12, 21, 1) 100%);
		#cursor {
			position: absolute;
			transform: translate(-50%, -50%);
			left: 50%;
			top: 50%;
			font-size: 2em;
			color: white;
		}
	}
</style>
