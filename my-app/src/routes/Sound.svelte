<script lang="ts">
	import { getContext } from 'svelte';

	const pads = Array(16).fill(0);
	const array = ["C2", "D2", "E2", "F2", "G2", "A2", "B2", "C3", "D3", "E3", "F3", "G3", "A3", "B3", "C4", "D4"];
	const { play } = getContext("audioContext") as any;
	const instruments = getContext("instruments") as any;

	const { top, bottom } = instruments;
</script>

<div class="grid">
	{#each pads as _, index}
		<button 
			class="pad"
			aria-label={'Play pad ' + (index + 1)}
			on:pointerdown={() => play(index)}>
			<span class="note">{array[index]}</span>
		</button>
	{/each}
</div>

<style>
	.grid {
		display: grid;
		grid-template-columns: repeat(4, 12rem);
		grid-gap: 12px;
	}

	.pad {
		width: 12rem;
		height: 12rem;
		background: #222;
		color: rgb(255, 1, 1);
		border: none;
		border-radius: 12px;
		box-shadow: 0 6px #000000;
		font-size: 18px;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		gap: 8px;
		cursor: pointer;
		transition: all 0.1s ease;
		font-weight: bold;
	}

	.pad:active {
		box-shadow: 0 2px #000000;
		transform: translateY(4px);
	}

	.pad:hover {
		background: #333;
	}

	.note {
		font-size: 28px;
		font-weight: bold;
	}

	.instruments {
		font-size: 12px;
		opacity: 0.8;
	}
</style>
