<script lang="ts">
	import { getContext } from 'svelte';
	import pianoImg from '$lib/images/piano.svg';
	import guitarImg from '$lib/images/guitar.svg';
	import fluteImg from '$lib/images/flute.svg';
	import cymbalImg from '$lib/images/cymbal.svg';

	export let isOpen = false;
	export let side = 'top'; // 'top' or 'bottom'

	const instruments = getContext('instruments') as any;
	const { top, bottom } = instruments;
	const currentStore = side === 'top' ? top : bottom;

	const instrumentList = [
		{ name: 'piano', image: pianoImg },
		{ name: 'guitar', image: guitarImg },
		{ name: 'flute', image: fluteImg },
		{ name: 'cymbal', image: cymbalImg }
	];

	function selectInstrument(instrumentName: string) {
		currentStore.set(instrumentName);
		isOpen = false;
	}

	function closeModal() {
		isOpen = false;
	}
</script>

{#if isOpen}
	<div 
		class="modal-overlay"
		on:click={closeModal}
		on:keydown={(e) => e.key === 'Escape' && closeModal()}
		role="presentation"
	>
		<div 
			class="modal-content" 
			on:click|stopPropagation
			on:keydown|stopPropagation
			role="dialog"
			aria-modal="true"
			aria-labelledby="modal-title"
			tabindex="0"
		>
			<button class="close-btn" on:click={closeModal}>✕</button>
			<h2 id="modal-title">Select Instrument</h2>
			<div class="instrument-grid">
				{#each instrumentList as instrument (instrument.name)}
					<button
						class="instrument-button"
						class:selected={$currentStore === instrument.name}
						on:click={() => selectInstrument(instrument.name)}
						aria-label="Select {instrument.name}"
					>
						<img src={instrument.image} alt={instrument.name} />
					</button>
				{/each}
			</div>
		</div>
	</div>
{/if}

<style>
	.modal-overlay {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background: rgba(0, 0, 0, 0.6);
		backdrop-filter: blur(4px);
		display: flex;
		justify-content: center;
		align-items: center;
		z-index: 1000;
	}

	.modal-content {
		background: linear-gradient(135deg, #1a1a1a 0%, #2d2d2d 100%);
		border-radius: 16px;
		padding: 40px;
		box-shadow: 0 20px 60px rgba(0, 0, 0, 0.8);
		max-width: 600px;
		width: 90%;
		position: relative;
		border: 2px solid #444;
	}

	.close-btn {
		position: absolute;
		top: 15px;
		right: 15px;
		background: none;
		border: none;
		color: #fff;
		font-size: 28px;
		cursor: pointer;
		width: 40px;
		height: 40px;
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 50%;
		transition: all 0.2s ease;
	}

	.close-btn:hover {
		background: rgba(255, 0, 0, 0.2);
		color: #ff6b6b;
	}

	h2 {
		color: #fff;
		text-align: center;
		margin: 0 0 30px 0;
		font-size: 24px;
		font-weight: bold;
	}

	.instrument-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
		gap: 20px;
	}

	.instrument-button {
		background: #222;
		border: 3px solid #444;
		border-radius: 12px;
		padding: 15px;
		cursor: pointer;
		transition: all 0.3s ease;
		display: flex;
		justify-content: center;
		align-items: center;
		aspect-ratio: 1;
	}

	.instrument-button:hover {
		background: #333;
		border-color: #666;
		transform: translateY(-4px);
		box-shadow: 0 8px 16px rgba(0, 0, 0, 0.5);
	}

	.instrument-button.selected {
		background: linear-gradient(135deg, #ff3e00 0%, #ff6b00 100%);
		border-color: #ff9500;
		box-shadow: 0 0 20px rgba(255, 62, 0, 0.5);
	}

	.instrument-button img {
		width: 80px;
		height: 80px;
		object-fit: contain;
		filter: brightness(1.1);
	}

	.instrument-button.selected img {
		filter: brightness(1.3);
	}
</style>
