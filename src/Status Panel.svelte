<script>
	import NumberFlow from '@number-flow/svelte';
	import { fade } from 'svelte/transition';
	import { calcPoints, inGoodPlace } from './shared.svelte';
	import { _prompt, ss } from './state.svelte';

	const count = $derived(ss.cells?.filter((c) => !inGoodPlace(c.index)).length || 0);
</script>

{#if ss.flip !== 1 && (ss.practice || (!ss.levelPrompt && !_prompt.id && !ss.playAgain))}
	<div class="status-panel quiet" in:fade>
		{#if count}
			<div in:fade><NumberFlow value={count} suffix=" cards mismatched" /></div>
		{:else}
			<span class={!ss.practice && !ss.fail ? 'loud' : ''} in:fade>
				{ss.practice ? 'Solved!' : ss.fail ? 'no points' : '+' + calcPoints()}
			</span>
		{/if}
	</div>
{:else}
	<div class="status-panel"></div>
{/if}

<style>
	.status-panel {
		grid-area: 4/1;
		place-self: center;
		display: grid;
		height: 36px;
		grid-auto-flow: column;
	}

	.quiet {
		font-size: 26px;
	}

	.loud {
		color: var(--gold);
		font-size: 32px;
	}
</style>
