<script lang="ts">
	import { getContext } from 'svelte';

	export let isOpen = false;
	let currentStep = 0;

	const tutorials = [
		{
			title: "Welcome to M.I.S.",
			subtitle: "Multi-Instrument Synthesizer",
			description: "Let's learn how to create amazing fused sounds!",
			highlight: null,
			action: "Next"
		},
		{
			title: "Step 1: Choose Your First Instrument",
			subtitle: "Top Selection",
			description: "Click on the instrument image on the left to open the selection menu. Choose from Piano, Guitar, Flute, or Cymbal.",
			highlight: "selector-top",
			action: "Next"
		},
		{
			title: "Step 2: Choose Your Second Instrument",
			subtitle: "Bottom Selection",
			description: "Click on the second instrument image below to select another instrument. You can pick the same instrument twice!",
			highlight: "selector-bottom",
			action: "Next"
		},
		{
			title: "Step 3: Watch the Fusion",
			subtitle: "The Magic Happens",
			description: "In the center, you'll see your two instruments fusing together with a unique generated name. The arrows show how they combine into one unified sound!",
			highlight: "fusion-display",
			action: "Next"
		},
		{
			title: "Step 4: Play the Notes",
			subtitle: "The 16-Pad Grid",
			description: "Click any of the 16 pads on the right to play notes on BOTH instruments at the same time. Press different pads to create melodies!",
			highlight: "pad-grid",
			action: "Next"
		},
		{
			title: "Step 5: Create Your Sound",
			subtitle: "Experiment & Explore",
			description: "Change instruments, mix different combinations, and click pads to create unique fused soundscapes. Have fun creating!",
			highlight: null,
			action: "Finish"
		}
	];

	function nextStep() {
		if (currentStep < tutorials.length - 1) {
			currentStep++;
		} else {
			closeTutorial();
		}
	}

	function closeTutorial() {
		isOpen = false;
		currentStep = 0;
	}

	function skipTutorial() {
		closeTutorial();
	}

	$: tutorial = tutorials[currentStep];
</script>

{#if isOpen}
	<div
		class="tutorial-overlay"
		on:click={closeTutorial}
		on:keydown={(e) => e.key === 'Escape' && closeTutorial()}
		role="presentation"
	>
		<div
			class="tutorial-modal"
			on:click|stopPropagation
			on:keydown|stopPropagation
			role="dialog"
			aria-modal="true"
			aria-labelledby="tutorial-title"
			tabindex="0"
		>
			<button class="close-btn" on:click={closeTutorial} aria-label="Close tutorial">✕</button>

			<div class="tutorial-content">
				<h2 id="tutorial-title">{tutorial.title}</h2>
				{#if tutorial.subtitle}
					<p class="subtitle">{tutorial.subtitle}</p>
				{/if}
				<p class="description">{tutorial.description}</p>
			</div>

			<div class="step-indicators">
				{#each tutorials as _, index}
					<div
						class="step-dot"
						class:active={index === currentStep}
						class:completed={index < currentStep}
					></div>
				{/each}
			</div>

			<div class="button-group">
				<button class="secondary-btn" on:click={skipTutorial}>Skip</button>
				<button class="primary-btn" on:click={nextStep}>
					{tutorial.action}
				</button>
			</div>
		</div>

		{#if tutorial.highlight}
			<div class="highlight-box" id={`highlight-${tutorial.highlight}`}></div>
		{/if}
	</div>
{/if}

<style>
	.tutorial-overlay {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background: rgba(0, 0, 0, 0.7);
		backdrop-filter: blur(4px);
		display: flex;
		justify-content: center;
		align-items: center;
		z-index: 999;
	}

	.tutorial-modal {
		background: linear-gradient(135deg, #1a1a1a 0%, #2d2d2d 100%);
		border-radius: 16px;
		padding: 40px;
		box-shadow: 0 20px 60px rgba(0, 0, 0, 0.9);
		max-width: 500px;
		width: 90%;
		position: relative;
		border: 2px solid #ff3e00;
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

	.tutorial-content {
		margin-bottom: 30px;
	}

	h2 {
		color: #fff;
		font-size: 26px;
		font-weight: bold;
		margin: 0 0 10px 0;
	}

	.subtitle {
		color: #ff3e00;
		font-size: 14px;
		font-weight: bold;
		text-transform: uppercase;
		letter-spacing: 1px;
		margin: 0 0 15px 0;
	}

	.description {
		color: #ccc;
		font-size: 16px;
		line-height: 1.6;
		margin: 0;
	}

	.step-indicators {
		display: flex;
		justify-content: center;
		gap: 8px;
		margin: 30px 0;
	}

	.step-dot {
		width: 10px;
		height: 10px;
		border-radius: 50%;
		background: #444;
		transition: all 0.3s ease;
		cursor: pointer;
	}

	.step-dot.active {
		background: #ff3e00;
		width: 30px;
		border-radius: 5px;
	}

	.step-dot.completed {
		background: #ff6b00;
	}

	.button-group {
		display: flex;
		gap: 12px;
		justify-content: flex-end;
	}

	.primary-btn {
		background: linear-gradient(135deg, #ff3e00 0%, #ff6b00 100%);
		color: #fff;
		border: none;
		border-radius: 8px;
		padding: 12px 28px;
		font-size: 14px;
		font-weight: bold;
		cursor: pointer;
		transition: all 0.3s ease;
	}

	.primary-btn:hover {
		transform: translateY(-2px);
		box-shadow: 0 8px 16px rgba(255, 62, 0, 0.4);
	}

	.secondary-btn {
		background: transparent;
		color: #ccc;
		border: 1px solid #555;
		border-radius: 8px;
		padding: 12px 28px;
		font-size: 14px;
		font-weight: bold;
		cursor: pointer;
		transition: all 0.3s ease;
	}

	.secondary-btn:hover {
		background: rgba(255, 255, 255, 0.05);
		color: #fff;
		border-color: #777;
	}

	.highlight-box {
		position: absolute;
		border: 3px solid #ff3e00;
		border-radius: 12px;
		box-shadow: 0 0 30px rgba(255, 62, 0, 0.6);
		pointer-events: none;
		animation: pulse-border 1.5s ease-in-out infinite;
	}

	@keyframes pulse-border {
		0%, 100% {
			box-shadow: 0 0 30px rgba(255, 62, 0, 0.6);
		}
		50% {
			box-shadow: 0 0 50px rgba(255, 62, 0, 0.8);
		}
	}
</style>
