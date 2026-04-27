<script lang="ts">
	import { onMount, getContext } from 'svelte';
	import pianoImg from '$lib/images/piano.svg';
	import guitarImg from '$lib/images/guitar.svg';
	import fluteImg from '$lib/images/flute.svg';
	import cymbalImg from '$lib/images/cymbal.svg';

	const instruments = getContext('instruments') as any;
	const { top, bottom } = instruments;

	let fusionImageUrl: string = '';
	let canvasRef: HTMLCanvasElement;

	const instrumentImages: Record<string, string> = {
		piano: pianoImg,
		guitar: guitarImg,
		flute: fluteImg,
		cymbal: cymbalImg
	};

	function generateFusionName(inst1: string, inst2: string): string {
		const combined = (inst1 + inst2).toLowerCase();
		const letters = combined.split('');
		const shuffled = letters.sort(() => Math.random() - 0.5);
		const fusedLength = Math.ceil(letters.length / 2);
		const fusedLetters = shuffled.slice(0, fusedLength);
		let fusedName = fusedLetters.join('').toUpperCase();
		fusedName = fusedName.substring(0, 8);
		return fusedName.charAt(0) + fusedName.slice(1).toLowerCase();
	}

	async function createFusionImage(topInstrument: string, bottomInstrument: string) {
		if (!canvasRef) return;

		const canvas = canvasRef;
		const ctx = canvas.getContext('2d');
		if (!ctx) return;

		try {
			const topImg = new Image();
			const bottomImg = new Image();

			topImg.onload = () => {
				bottomImg.onload = () => {
					ctx.fillStyle = '#1a1a1a';
					ctx.fillRect(0, 0, 200, 200);

					ctx.drawImage(topImg, 0, 0, 200, 100, 0, 0, 200, 100);
					ctx.drawImage(bottomImg, 0, 100, 200, 100, 0, 100, 200, 100);

					fusionImageUrl = canvas.toDataURL('image/png');
				};

				bottomImg.src = instrumentImages[bottomInstrument];
			};

			topImg.src = instrumentImages[topInstrument];
		} catch (error) {
			console.error('Error creating fusion image:', error);
		}
	}

	$: if ($top && $bottom) {
		createFusionImage($top, $bottom);
	}

	$: fusionName = generateFusionName($top, $bottom);
</script>

<div class="fusion-container">
	<div class="fusion-display">
		<canvas bind:this={canvasRef} width="200" height="200" style="display: none;"></canvas>

		{#if fusionImageUrl}
			<img src={fusionImageUrl} alt="Fused instrument" class="fusion-icon" />
		{:else}
			<div class="fusion-icon-placeholder">Creating...</div>
		{/if}

		<div class="fusion-name">
			<h3>{fusionName}</h3>
			<p>Fusion</p>
		</div>
	</div>
</div>

<style>
	.fusion-container {
		width: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 20px 0;
	}

	.fusion-display {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 15px;
	}

	.fusion-icon {
		width: 140px;
		height: 140px;
		border-radius: 12px;
		filter: drop-shadow(0 0 20px rgba(255, 62, 0, 0.4));
		animation: pulse 2s ease-in-out infinite;
		border: 2px solid #ff3e00;
	}

	.fusion-icon-placeholder {
		width: 140px;
		height: 140px;
		display: flex;
		justify-content: center;
		align-items: center;
		background: #222;
		border: 2px solid #ff3e00;
		border-radius: 12px;
		color: #999;
		font-size: 12px;
	}

	@keyframes pulse {
		0%, 100% {
			filter: drop-shadow(0 0 20px rgba(255, 62, 0, 0.4));
		}
		50% {
			filter: drop-shadow(0 0 30px rgba(255, 62, 0, 0.6));
		}
	}

	.fusion-name {
		text-align: center;
		color: #fff;
	}

	.fusion-name h3 {
		margin: 0;
		font-size: 24px;
		font-weight: bold;
		color: #ff3e00;
		text-shadow: 0 0 10px rgba(255, 62, 0, 0.5);
		letter-spacing: 1px;
	}

	.fusion-name p {
		margin: 5px 0 0 0;
		font-size: 12px;
		color: #999;
		text-transform: uppercase;
		letter-spacing: 2px;
	}
</style>
