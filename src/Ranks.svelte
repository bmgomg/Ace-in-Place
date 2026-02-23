<script>
	import { ss } from './state.svelte';

	const info = 'Jack = 11   Queen = 12   King = 13   Ace = 14';
</script>

{#if ss.practice}
	<div class="ranks">{info}</div>
{/if}

<style>
	.ranks {
		grid-area: 5/1;
		place-self: center;
		display: grid;
        font-size: 18px;
        font-style: italic;
        opacity: 0.65;
	}
</style>
