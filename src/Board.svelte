<script>
	import { fade } from 'svelte/transition';
	import Cell from './Cell.svelte';
	import { CELL_COUNT, CELL_HEIGHT, CELL_MARGIN, SZY } from './const';
	import { makePuzzle } from './shared.svelte';
	import { ss } from './state.svelte';
	import { _range } from './utils';

	let _this = $state(null);

	$effect(() => {
		const onTransitionEnd = (e) => {
			if (e.propertyName !== 'transform') {
				return;
			}

			if (ss.flip) {
				delete ss.flip;
				ss.ticks = 0;
				makePuzzle();
			}
		};

		_this?.addEventListener('transitionend', onTransitionEnd);
		return () => _this?.removeEventListener('transitionend', onTransitionEnd);
	});
</script>

{#if ss.cells && (ss.practice || !ss.levelPrompt)}
	<div bind:this={_this} class="board {ss.flip ? 'flipped' : ''}" in:fade>
		{#each _range(1, CELL_COUNT) as index (index)}
			<Cell cell={ss.cells[index - 1]} />
		{/each}
	</div>
{:else}
	<div style="grid-area: 3/1; height: {(CELL_HEIGHT + CELL_MARGIN * 2) * SZY}px;"></div>
{/if}

<style>
	.board {
		grid-area: 3/1;
		place-self: center;
		display: grid;
		transition: linear transform 0.5s;
	}

	.flipped {
		transform: rotateY(90deg);
	}
</style>
