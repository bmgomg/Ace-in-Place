<script>
	import { fade } from 'svelte/transition';
	import { ss } from './state.svelte';
	import TextButton from './Text Button.svelte';
	import { persist } from './shared.svelte';

	const onOp = (large) => {
		ss.largePractice = large;
        persist();

        ss.flip = 1;
	};
</script>

{#if ss.practice && ss.flip !== 1}
	<div class="ops" transition:fade>
        <TextButton id='tb-practice-small' text={['3×4']} selected={!ss.largePractice} disabled={!ss.largePractice} style='color: var(--ow);' onClick={() => onOp(false)}/>
        <TextButton id='tb-practice-large' text={['4×5']} selected={ss.largePractice}  disabled={ss.largePractice} style='color: var(--ow);' onClick={() => onOp(true)} />
	</div>
{/if}

<style>
	.ops {
		grid-area: 1/1 / span 2/1;
		place-self: center;
		display: grid;
		grid-auto-flow: column;
		gap: 50px;
		font-family: Cinzel;
		font-size: 36px;
	}
</style>
