<script>
	import { fade } from 'svelte/transition';
	import { MAX_STRIKES } from './const';
	import { ss } from './state.svelte';

	let h = null;
</script>

{#if !ss.practice && ss.strikes === MAX_STRIKES}
	<div class="go" bind:clientHeight={h} style="border-radius: {h / 2}px" in:fade>GAME OVER!</div>
{/if}

<style>
	.go {
		grid-area: 3/1;
		place-self: center;
		display: grid;
		padding: 10px 20px;
		background: #202020e0;
		font-size: 24px;
		z-index: 1;
		border: 2px solid var(--ow);
		box-sizing: border-box;
	}
</style>
