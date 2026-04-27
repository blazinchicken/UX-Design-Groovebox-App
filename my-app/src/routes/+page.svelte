<script lang="ts"> 
	import { onMount, setContext } from 'svelte';
	import { writable } from 'svelte/store';
	import * as Tone from "tone";
	import Selector from './Selector.svelte';
	import Sound from './Sound.svelte';
	import InstrumentModal from './InstrumentModal.svelte';
	import FusionDisplay from './FusionDisplay.svelte';
	import Tutorial from './Tutorial.svelte';
	
	let synths: Record<string, any> = {};
	let array = ["C2", "D2", "E2", "F2", "G2", "A2", "B2", "C3", "D3", "E3", "F3", "G3", "A3", "B3", "C4", "D4"];
	let note: string | null = null;
	let openTopModal = false;
	let showTutorial = false;
	let openBottomModal = false;


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
				modulation: {
					type: "square"
				}
			}).toDestination(),
			cymbal: new Tone.MetalSynth({
				envelope: {
					attack: 0.001,
					decay: 1.4,
					sustain: 0.01,
					release: 1.4
				},
				harmonicity: 5.1,
				modulationIndex: 32,
				resonance: 4000,
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

	async function play(index: number) {
		await Tone.start();

		const currentInstrument1 = $top; 
		const currentInstrument2 = $bottom; 
		const synth: any = synths[currentInstrument1];
		const synth2: any = synths[currentInstrument2];
		console.log("Playing combined sound: " + currentInstrument1 + " + " + currentInstrument2);

		if (!synth) return;
		if (!synth2) return;

		if (currentInstrument1 === currentInstrument2) {
			synth.triggerAttackRelease(array[index], "4n");
			console.log("Played note: " + array[index] + " on " + currentInstrument1);
			return;
		} else {
			synth.triggerAttackRelease(array[index], "4n");
			synth2.triggerAttackRelease(array[index], "4n");
			console.log("Played note: " + array[index] + " on " + currentInstrument1 + " + " + currentInstrument2);
		}
	}

	function openModal(side: string) {
		if (side === 'top') {
			openTopModal = true;
		} else {
			openBottomModal = true;
		}
	}

	setContext("instruments", instruments);
	setContext("audioContext", { play, openModal });
</script>
<div class="layout">
	<div class="left">
		<div class="lefttop">
			<Selector side="top" />
		</div>
		<div class="fusion-section">
			<FusionDisplay />
		</div>
		<div class="leftmid">
			<Selector side="bottom" />
		</div>
		<div class="leftbottom">
			<h3>M.I.S. </h3>
			<h2>Multi-Instrument Synthesizer</h2>
			<button class="tutorial-btn" on:click={() => (showTutorial = true)}>? Tutorial</button>
		</div>
	</div>
	<div class="right">
		<Sound />
	</div>

</div>

<InstrumentModal bind:isOpen={openTopModal} side="top" />
<InstrumentModal bind:isOpen={openBottomModal} side="bottom" />
<Tutorial bind:isOpen={showTutorial} />

<style>
	.layout {
		display: grid;
		grid-template-columns: 1fr 2fr;
		height:100vh;
		width: 100%;
	}
	.left {
		display: grid;
		grid-template-rows: 1fr 1.2fr 1fr 1.5fr;
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

	.fusion-section {
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 10px 0;
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
		flex-direction: column;
		justify-content: flex-start;
		align-items: flex-start;
		gap: 15px;
	}

	.tutorial-btn {
		background: linear-gradient(135deg, #ff3e00 0%, #ff6b00 100%);
		color: #fff;
		border: none;
		border-radius: 8px;
		padding: 10px 20px;
		font-size: 14px;
		font-weight: bold;
		cursor: pointer;
		transition: all 0.3s ease;
		margin-top: 10px;
	}

	.tutorial-btn:hover {
		transform: translateY(-2px);
		box-shadow: 0 8px 16px rgba(255, 62, 0, 0.4);
	}

	.tutorial-btn:active {
		transform: translateY(0);
	}

	.right {
		display: flex;
		padding: 30px;
		justify-content: center;
		align-items: center;
		background: #c2baba;
	}
</style>


