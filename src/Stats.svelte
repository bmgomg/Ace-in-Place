<script>
	import NumberFlow from '@number-flow/svelte';
	import { _stats, ss } from './state.svelte';
	import { MODE_CHALLENGE } from './const';

	const ave = $derived(_stats.plays ? Math.round(_stats.total / _stats.plays) : 0);
</script>

{#if !ss.practice}
	<div class="stats">
		<span class="label">PLAYS</span>
		<div class="value"><NumberFlow value={_stats.plays} /></div>
		<span></span>
		<span class="label">AVE</span>
		{#if _stats.plays}
			<div class="value"><NumberFlow value={ave} format={{ useGrouping: false }} /></div>
		{:else}
			<div class="bullet">•</div>
		{/if}
		<span></span>
		<span class="label">BEST</span>
		{#if _stats.plays}
			<div class="value"><NumberFlow value={_stats.best} format={{ useGrouping: false }} /></div>
		{:else}
			<span>•</span>
		{/if}
	</div>
{/if}

<style>
	.stats {
		grid-area: 1/1;
		place-self: center;
		font-size: 16px;
		display: grid;
		grid-auto-flow: column;
		align-items: baseline;
		gap: 10px;
		filter: drop-shadow(0 0 3px black);
	}

	.label {
		opacity: 0.8;
	}

	.value {
		color: white;
		font-family: Cinzel;
	}
</style>
