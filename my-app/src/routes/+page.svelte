<script lang="ts"> 
	import { onMount, setContext } from 'svelte';
	import { writable } from 'svelte/store';
	import * as Tone from "tone";
	import Selector from './Selector.svelte';

	import InstrumentModal from './InstrumentModal.svelte';
	import FusionDisplay from './FusionDisplay.svelte';
	import Tutorial from './Tutorial.svelte';

	
	let synths: Record<string, any> = {};
	let noteArray = ["C2", "D2", "E2", "F2", "G2", "A2", "B2", "C3", "D3", "E3", "F3", "G3", "A3", "B3", "C4", "D4"];
	let note: string | null = null;
	let openTopModal = false;
	let showTutorial = false;
	let openBottomModal = false;
	let rank = 1;
	
	import { getContext } from 'svelte';


	let pads = Array(16).fill(0);
	let padArray = ["C2", "D2", "E2", "F2", "G2", "A2", "B2", "C3", "D3", "E3", "F3", "G3", "A3", "B3", "C4", "D4"];




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
			synth.triggerAttackRelease(noteArray[index], "4n");
			console.log("Played note: " + noteArray[index] + " on " + currentInstrument1);
			return;
		} else {
			synth.triggerAttackRelease(noteArray[index], "4n");
			synth2.triggerAttackRelease(noteArray[index], "4n");
			console.log("Played note: " + noteArray[index] + " on " + currentInstrument1 + " + " + currentInstrument2);
		}
	}

	function openModal(side: string) {
		if (side === 'top') {
			openTopModal = true;
		} else {
			openBottomModal = true;
		}
	}

	function changeNote(){
		const noteToChange = parseInt(padSelect.value);
		const newNote = noteSelect.value;
		noteArray[noteToChange - 1] = newNote;
		padArray[noteToChange - 1] = newNote;
	
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
		<p>
			<select id="padSelect">
				<option value="1">1</option>
				<option value="2">2</option>
				<option value="3">3</option>
				<option value="4">4</option>
				<option value="5">5</option>
				<option value="6">6</option>
				<option value="7">7</option>
				<option value="8">8</option>
				<option value="9">9</option>
				<option value="10">10</option>
				<option value="11">11</option>
				<option value="12">12</option>
				<option value="13">13</option>
				<option value="14">14</option>
				<option value="15">15</option>
				<option value="16">16</option>
			</select>
			
			<select id="noteSelect">
				<option value="F1">F1</option>
				<option value="G1">G1</option>
				<option value="A1">A1</option>
				<option value="B1">B1</option>
				<option value="C2">C2</option>
				<option value="D2">D2</option>
				<option value="E2">E2</option>
				<option value="F2">F2</option>
				<option value="G2">G2</option>
				<option value="A2">A2</option>
				<option value="B2">B2</option>
				<option value="C3">C3</option>
				<option value="D3">D3</option>
				<option value="E3">E3</option>
				<option value="F3">F3</option>
				<option value="G3">G3</option>
				<option value="A3">A3</option>
				<option value="B3">B3</option>
				<option value="C4">C4</option>
				<option value="D4">D4</option>
				<option value="E4">E4</option>
				<option value="F4">F4</option>
				<option value="G4">G4</option>
				<option value="A4">A4</option>
				<option value="B4">B4</option>
				<option value="C5">C5</option>
				<option value="D5">D5</option>
			</select>
		</p>
		<button on:click={changeNote}>
		Change Note
		</button>
		
		</div> 
	</div>
	<div class="right">
		<div class="grid-{rank}">
			{#each pads as _, index}
				<button 
					class="pad"
			
					aria-label={'Play pad ' + (index + 1)}
					on:pointerdown={() => play(index)}>
					<span class="note">{padArray[index]}</span>
				</button>
			{/each}
		</div>
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
	
		.grid-1 {
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


