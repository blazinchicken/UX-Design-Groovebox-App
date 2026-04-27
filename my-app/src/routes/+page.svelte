<script>
	import { onMount, setContext } from 'svelte';
	import { writable } from 'svelte/store';
	import * as Tone from "tone";
	import Selector from './Selector.svelte';
	import { useFloating, flip } from "@skeletonlabs/floating-ui-svelte";

	let synths = {};
	const pads = Array(16).fill(0);
	var note = null;
	let array = ["C2", "D2", "E2", "F2", "G2", "A2", "B2", "C3", "D3", "E3", "F3", "G3", "A3", "B3", "C4", "D4"];
	let selected1 = null;
	let selected2 = null;

	let topFloatingEl;
	let bottomFloatingEl;

	let showTopFloating = false;
	let showBottomFloating = false;

	const topFloating = useFloating({
		placement: "top",
		middleware: [flip()],
		strategy: "fixed"
	});

	const bottomFloating = useFloating({
		placement: "top",
		middleware: [flip()],
		strategy: "fixed"
	});

	onMount(() => {
		console.log("Component mounted");
		synths = {
			piano: new Tone.Synth({}).toDestination(),
			guitar: new Tone.PluckSynth({
				resonance: 0.9,
				dampening: 2000,
			}).toDestination(),
			flute: new Tone.FMSynth({
				harmonicity: 3,
				modulationIndex: 10,
				envelope: {
					attack: 0.01,
					decay: 0.1,
					sustain: 0.5,
					release: 1
				},
				octaves: 2.5,
				modulator: {
					type: "square"
				}
			}).toDestination(),
			cymbal: new Tone.MetalSynth({
				frequency: 200,
				envelope: {
					attack: 0.001,
					decay: 1.4,
					sustain: 0.01,
					release: 1.4
				},
				harmonicity: 5.1,
				modulationIndex: 32,
				resonance: 4000,
				octaves: 1.5,
				volume: -10
			}).toDestination()

		};
		note = "A2";

	});

	const instruments = {
		top: writable("piano"),
		bottom: writable("guitar")
	};

	const { top, bottom } = instruments;

	async function play(index) {
		await Tone.start();

		const currentInstrument1 = $top; 
		const currentInstrument2 = $bottom; 
		const synth = synths[currentInstrument1];
		const synth2 = synths[currentInstrument2];
		const currentInstrument = $top;
		const synth = synths[currentInstrument];
		console.log("Audio is ready");

		if (!synth) return;
		if (!synth2) return;

		if (currentInstrument1 === currentInstrument2) {
			synth.triggerAttackRelease(array[index], "4n");
			console.log("Played note: " + array[index] + " on " + currentInstrument1);
			return;
		} else {
		synth.triggerAttackRelease(array[index], "4n");
		synth2.triggerAttackRelease(array[index], "4n");
		console.log("Played note: " + array[index] + " on " + currentInstrument1);
		}
	}

	setContext("instruments", instruments);
</script>
<div class="layout">
	<div class="left">
		<div class="lefttop">
			<button class="img-btn main-btn" on:click={()=> {
				showTopFloating = true;
				showBottomFloating = false;
				}}>
				<img src={`/${$top}.png`} alt={$top} />
				<span>Top</span>
			</button>
		</div>
		<div class="leftmid">
			<button class="img-btn main-btn" on:click={()=> {
				showBottomFloating = true;
				showTopFloating = false;
				}}>
				<img src={`/${$bottom}.png`} alt={$bottom} />
				<span>Bottom</span>
			</button>
		</div>
		<div class="leftbottom">
			<h3>M.I.S. </h3>
			<h2>Multi-Instrument Synthesizer</h2>
		</div>
	</div>
	<div class="right">
		<div class="grid">
			{#each pads as _, index}
			<button aria-label={'Play pad ' + (index + 1)}
				on:pointerdown={() => play(index)}>
					{array[index]}
				</button>
			{/each}
		</div>
	</div>
{#if showTopFloating}
<div bind:this={topFloating.elements.floating} style="" class="floating" >

	<button class="img-btn" on:click={() => {
	instruments.top.set("piano");
	showTopFloating = false;
}}>
	<img src="/piano.png" alt="Piano" />
</button>

	<button class="img-btn" on:click={() => {
	instruments.top.set("guitar");
	showTopFloating = false;
}}>
	<img src="/guitar.png" alt="Guitar" />
</button>

	<button on:click={() => showTopFloating = false}>
		Close
	</button>
</div>

{/if}

{#if showBottomFloating}
<div bind:this={bottomFloating.elements.floating} style="" class="floating" >

	<button class="img-btn" on:click={() => {
	instruments.bottom.set("piano");
	showBottomFloating = false;
}}>
	<img src="/piano.png" alt="Piano" />
</button>

	<button class="img-btn" on:click={() => {
	instruments.bottom.set("guitar");
	showBottomFloating = false;
}}>
	<img src="/guitar.png" alt="Guitar" />
</button>

	<button on:click={() => showBottomFloating = false}>
		Close
	</button>
</div>

{/if}
{#if showTopFloating || showBottomFloating}
<div class="backdrop"></div>
{/if}

{#if showTopFloating || showBottomFloating}
	<div class="backdrop" on:click={() => {
		showTopFloating = false;
		showBottomFloating = false;
	}}></div>
{/if}

</div>


<style>
	.layout {
		display: grid;
		grid-template-columns: 1fr 2fr;
		height:100vh;
		width: 100%;
	}
	.left {
		display: grid;
		grid-template-rows: 1fr 1fr 2fr;
		background: #080808;
		padding: 30px;
		color: white;
	}
	.lefttop {
		font-size: 24px;
		font-weight: bold;
		display: flex;
		justify-content: flex-start;
		align-items: flex-start;
	}

	.leftmid {
		font-size: 24px;
		font-weight: bold;
		display: flex;
		justify-content: flex-start;
		align-items: center;
	}
	.leftbottom {
		font-size: 24px;
		font-weight: bold;
		display: flex;
		justify-content: flex-start;
		align-items: flex-end;
	}

	.right {
		display: flex;
		padding: 30px;
		justify-content: center;
		align-items: center;
		background: #c2baba;
	}
	.grid {
		display: grid;
		grid-template-columns: repeat(4, 12rem);
		grid-gap: 12px;
	}

	button {
		width: 12rem;
		height: 12rem;
		background: #222;
		color: rgb(255, 1, 1);
		border: none;
		border-radius: 12px;
		box-shadow: 0 6px #000000;
		font-size: 18px;
	}
	.floating button {
		width: 100%;
		height: auto;
		padding: 10px;
		font-size: 16px;
		box-shadow: none;
	}
	.floating {
		position: fixed;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		z-index: 1000;
		width: 320px;
		background: #111;
		padding: 20px;
		border-radius: 12px;
		display: flex;
		flex-direction: column;
		gap: 10px;
	}
	.backdrop {
		position: fixed;
		inset: 0;
		background: rgba(0, 0, 0, 0.3);
		backdrop-filter: blur(6px);
		z-index: 999;
	}
	.img-btn {
	width: 100%;
	height: auto;
	background: none;
	box-shadow: none;
	padding: 0;
	border: none;
	cursor: pointer;
}
.img-btn img {
	width: 100%;
	height: auto;
	object-fit: contain;
	border-radius: 8px;
}
</style>