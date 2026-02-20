<script>
	import { sample } from 'lodash-es';
	import { fade } from 'svelte/transition';
	import { CELL_HEIGHT, CELL_MARGIN, LEVEL_SECS, SZY } from './const';
	import { onStart } from './shared.svelte';
	import { _prompt, ss } from './state.svelte';
	import TextButton from './Text Button.svelte';

	const ul = '<ul style="margin: 15px 0 0 0;">';
	const li = '<li style="margin: 10px 0 0 -20px;">';
	const hi = '<span style="color: var(--gold);">';

	const L1 = `
        <ul>
        ${li}You have ${hi}${LEVEL_SECS[0]} seconds</span> to solve each puzzle with a ${hi}single swap</span>.</li>
        ${li}If you make an ${hi}incorrect</span> swap or run ${hi}out of time</span>, you earn a ${hi}strike</span>.</li>
        ${li}Three strikes end the game.</li>
        </ul>`;

	const L2 = `
		<span>So far so good!</span>
        ${ul}
        ${li}Your ${hi}strike</span> count has been ${hi}reset</span>.</li>
        ${li}You now have only ${hi}${LEVEL_SECS[1]} seconds</span> to solve each puzzle.</li>
        </ul>`;

	const L3 = `
		<span>Great!</span>
        ${ul}
        ${li}Your ${hi}strike</span> count has been ${hi}reset</span>.</li>
        ${li}You now have only ${hi}${LEVEL_SECS[2]} seconds</span> to solve each puzzle.</li>
        </ul>`;

	const L4 = `
		<span>Excellent!</span>
        ${ul}
        ${li}Your ${hi}strike</span> count has been ${hi}reset</span>.</li>
        ${li}You now have only ${hi}${LEVEL_SECS[3]} seconds</span> to solve each puzzle.</li>
        </ul>`;

	const CHEERS = ['Phenomenal!', 'Amazing!', 'Incredible!', 'Unbelievable!', 'Fantastic!', 'Astounding!', 'Extraordinary!', 'Sensational!'];

	const L5 = $derived(`
		<span>${ss.level > LEVEL_SECS.length ? sample(CHEERS) : 'Impressive!'}</span>
        ${ul}
        ${li}Your ${hi}strike</span> count has been ${hi}reset</span>.</li>
        ${li}You ${ss.level > LEVEL_SECS.length ? 'still have' : 'now have only'} ${hi}${LEVEL_SECS[4]} seconds</span> to solve each puzzle.</li>
        </ul>`);

	const CONTENTS = $derived([L1, L2, L3, L4, L5]);

	const onClick = () => {
		onStart(ss.ticks ? 'plop' : 'dice');
		delete ss.levelPrompt;
	};
</script>

{#if !ss.practice && ss.levelPrompt}
	<div class="level-prompt" style="height: {(CELL_HEIGHT + CELL_MARGIN * 2) * SZY + 46}px;" in:fade>
		{#if ss.ticks}
			<span class="progress" tabindex="-1">game in progress</span>
		{:else}
			<div class="content" tabindex="-1">
				<!-- eslint-disable-next-line svelte/no-at-html-tags -->
				{@html CONTENTS[Math.min(ss.level, LEVEL_SECS.length) - 1]}
			</div>
		{/if}
		<div class="button {_prompt.opacity ? 'hidden' : ''}">
			<TextButton id="level-prompt" text={[ss.ticks ? 'resume' : ss.level > 1 ? 'continue' : 'start']} {onClick} />
		</div>
	</div>
{/if}

<style>
	.level-prompt {
		grid-area: 3/1 / span 2/1;
		display: grid;
		place-self: center;
		place-content: center;
		gap: 50px;
	}

	.content {
		display: grid;
		align-content: start;
		width: 360px;
		font-size: 22px;
	}

	.progress {
		display: grid;
		align-content: center;
		font-family: Cinzel;
		font-size: 22px;
	}

	.button {
		font-family: Cinzel;
		font-size: 28px;
		transition: opacity 0.3s;
	}

	.hidden {
		opacity: 0;
		pointer-events: none;
	}
</style>
