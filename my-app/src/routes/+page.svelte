<script> 
	import { onMount, setContext } from 'svelte';
	import { writable } from 'svelte/store';
	import * as Tone from "tone";
	import Selector from './Selector.svelte';
	
	let synths = {};
	const pads = Array(16).fill(0);
	var note = null;
	let array = ["C2", "D2", "E2", "F2", "G2", "A2", "B2", "C3", "D3", "E3", "F3", "G3", "A3", "B3", "C4", "D4"];
	let selected1 = null;
	let selected2 = null;

	onMount(() => {	
		console.log("Component mounted");
		synths = {
			piano: new Tone.Synth().toDestination(),
			guitar: new Tone.PluckSynth().toDestination()
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

		const currentInstrument = $top; 
		const synth = synths[currentInstrument];
		console.log("Audio is ready");

		if (!synth) return;

		synth.triggerAttackRelease(array[index], "8n");
		console.log("Played note: " + array[index] + " on " + currentInstrument);
	}

	setContext("instruments", instruments);
</script>
<div class="layout">
	<div class="left">
		<div class="lefttop">
			<Selector side="top" />
		</div>
		<div class="leftmid">
			<Selector side="bottom" />
		</div>
		<div class="leftbottom">
			<h3>M.I.S. </h3>
				<h2>Multi-Instrument Synthesizer</h2>
		</div>
	</div>
	<div class="right">
		<div class="grid">
			{#each pads as _, index}
				<button 
				aria-label={'Play pad ' + (index + 1)}
				on:pointerdown={() => play(index)}>
					{array[index]}
				</button>
			{/each}
		</div>
	</div>

</div>

<style>
	.layout {
		display: grid;
		grid-template-columns: 1fr 2fr;
		height:100vh;
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
		grid-template-columns: repeat(4, 160px);
		grid-gap: 12px;
	}

	button {
		width: 160px;
		height: 160px;
		background: #222;
		color: rgb(255, 1, 1);
		border: none;
		border-radius: 12px;
		box-shadow: 0 6px #000000;
		font-size: 18px;
	}
</style>


