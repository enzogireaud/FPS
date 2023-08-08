<script lang="ts">
	import { Octree } from 'three/examples/jsm/math/Octree';
	import Map from './Map.svelte';
	import Player from './Player.svelte';

	// Init Octree (colisions)
	const worldOctree = new Octree();
</script>

<Map {worldOctree} />
<Player {worldOctree} />
