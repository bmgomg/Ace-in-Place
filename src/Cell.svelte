<script>
	import { CARDS } from './Cards';
	import { CELL_MARGIN, CELL_WIDTH, MAX_STRIKES, SZX } from './const';
	import { calcPoints, inGoodPlace, isSolved, onFail, onTaskCompleted, persist, stopTimer } from './shared.svelte';
	import { _sound } from './sound.svelte';
	import { _prompt, ss } from './state.svelte';
	import { post, rectCenter } from './utils';

	const { cell } = $props();
	const { index, code } = $derived(cell);
	let _this = $state(null);
	const row = $derived(Math.floor(index / SZX) + 1);
	const col = $derived((index % SZX) + 1);

	const img = $derived(CARDS[+code]);

	const onPointerDown = () => {
		_prompt.opacity = 0;

		_sound.play('click');

		if (ss.swap1 === index) {
			delete ss.swap1;
			return;
		}

		if (ss.swap1 + 1) {
			ss.swap2 = index;
		} else {
			ss.swap1 = index;
		}
	};

	$effect(() => {
		const onTransitionEnd = (e) => {
			if (e.propertyName !== 'translate') {
				return;
			}

			if (index === ss.swap1 && ss.swap2 + 1) {
				_sound.play('cluck');

				const ch1 = ss.cells[ss.swap1].code;
				const ch2 = ss.cells[ss.swap2].code;
				ss.cells[ss.swap1].code = ch2;
				ss.cells[ss.swap2].code = ch1;

				delete ss.swap1;
				delete ss.swap2;

				if (isSolved()) {
					post(() => _sound.play('won', { rate: 2 }), 150);

					if (ss.timer) {
						onTaskCompleted();
						ss.points += calcPoints();

						persist();
					}

					post(() => (ss.flip = true), 1000);
				} else if (ss.timer) {
					onFail();
				}

				stopTimer();
			}
		};

		_this.addEventListener('transitionend', onTransitionEnd);
		return () => _this.removeEventListener('transitionend', onTransitionEnd);
	});

	const zi = $derived(index === ss.swap1 || index === ss.swap2 ? 1 : 0);
	const duration = $derived(ss.swap2 + 1 ? '0.5s' : '0s');
	const bgx = $derived(inGoodPlace(index) | ss.home || ss.delay || (ss.timer && ss.ticks) ? '' : 'bg-fail');

	const off = $derived.by(() => {
		if (!(ss.swap1 + 1) || !(ss.swap2 + 1)) {
			return [0, 0];
		}

		if (index !== ss.swap1 && index !== ss.swap2) {
			return [0, 0];
		}

		const r1 = rectCenter('#cell-' + ss.swap1);
		const r2 = rectCenter('#cell-' + ss.swap2);

		if (index === ss.swap1) {
			return [r2.x - r1.x, r2.y - r1.y];
		}

		return [r1.x - r2.x, r1.y - r2.y];
	});

	const disabled = $derived((ss.swap1 + 1 && ss.swap2 + 1) || (!ss.practice && ss.strikes === MAX_STRIKES) || isSolved());
</script>

<div
	bind:this={_this}
	id={'cell-' + index}
	class="cell {isSolved() ? 'short-pulse' : ''} {disabled ? 'disabled' : index === ss.swap1 && !(ss.swap2 + 1) ? 'pulse' : ''}"
	style="grid-area: {row}/{col}; width: {CELL_WIDTH}px; margin: {CELL_MARGIN}px; translate: {off[0]}px {off[1]}px; transition-duration: {duration}; z-index: {zi};"
	onpointerdown={onPointerDown}
>
	<div class="bg {bgx}">
		<img src={img} alt="" width="100%" />
	</div>
</div>

<style>
	.cell {
		display: grid;
		aspect-ratio: 0.72;
		box-sizing: border-box;
		border-radius: 5px;
		place-content: center;
		font-size: 36px;
		cursor: pointer;
		background: var(--ow);
		transition: translate 0.5s linear;
	}

	.bg {
		display: grid;
		place-content: center;
		border-radius: 5px;
		transition: background-color 0.5s;
	}

	.bg-fail {
		background: #ff000020;
	}

	.disabled {
		pointer-events: none;
	}

	.pulse {
		animation: pulse 0.2s alternate infinite ease-in-out;
	}

	.short-pulse {
		animation: pulse 0.2s alternate 4 ease-in-out 0.2s;
	}

	@keyframes pulse {
		from {
			transform: scale(1);
		}
		to {
			transform: scale(0.90);
		}
	}
</style>
