<script>
	import Board from './Board.svelte';
	import GameOver from './Game Over.svelte';
	import LevelPrompt from './Level Prompt.svelte';
	import Prompt from './Prompt.svelte';
	import Scoreboard from './Scoreboard.svelte';
	import { elapsedSecs, secsRemained } from './shared.svelte';
	import { ss } from './state.svelte';
	import Stats from './Stats.svelte';
	import StatusPanel from './Status Panel.svelte';
	import Timer from './Timer.svelte';
	import Toolbar from './Toolbar.svelte';

	const hidden = $derived(ss.home);
</script>

<div class="game-page {hidden ? 'hidden' : ''}">
	<Stats />
	<Scoreboard />
	<LevelPrompt/>
	<Board />
	<GameOver />
	<StatusPanel />
	<Timer secs={!ss.practice ? secsRemained() : elapsedSecs()} />
	<Prompt/>
	<Toolbar />
</div>

<style>
	.game-page {
		grid-area: 1/1;
		display: grid;
		grid: 0.3fr 1fr auto 0.3fr 0.8fr auto / auto;
		gap: 10px;
		width: 100%;
		transition: opacity 0.2s;
		box-sizing: border-box;
		padding: 10px 0;
		user-select: none;
	}

	.hidden {
		opacity: 0;
		pointer-events: none;
	}
</style>
