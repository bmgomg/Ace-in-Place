<script>
	import { fade } from 'svelte/transition';
	import Cell from './Cell.svelte';
	import { CELL_HEIGHT, CELL_MARGIN, MODE_PRACITCE } from './const';
	import { makePuzzle, onMode } from './shared.svelte';
	import { ss } from './state.svelte';
	import { _range } from './utils';

	let _this = $state(null);

	$effect(() => {
		const onTransitionEnd = (e) => {
			if (e.propertyName !== 'transform') {
				return;
			}

			if (ss.flip) {
				const flip = ss.flip;
				delete ss.flip;
				ss.ticks = 0;

				if (flip === 1) {
					onMode(MODE_PRACITCE);
				} else {
					makePuzzle();
				}
			}
		};

		_this?.addEventListener('transitionend', onTransitionEnd);
		return () => _this?.removeEventListener('transitionend', onTransitionEnd);
	});
</script>

{#if ss.cells && (ss.practice || !ss.levelPrompt)}
	<div bind:this={_this} class="board {ss.flip ? 'flipped' : ''}" in:fade>
		{#each _range(1, ss.cellCount) as index (index)}
			<Cell cell={ss.cells[index - 1]} />
		{/each}
	</div>
{:else}
	<div style="grid-area: 3/1; height: {(CELL_HEIGHT + CELL_MARGIN * 2) * ss.rows}px;"></div>
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
