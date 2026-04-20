<!-- <script lang="ts">
	import welcomeFallback from '$lib/images/svelte-welcome.png';
	import welcome from '$lib/images/svelte-welcome.webp';

	import Counter from './Counter.svelte';
</script>

<svelte:head>
	<title>Home</title>
	<meta name="description" content="Svelte demo app" />
</svelte:head>

<section>
	<h1>
		<span class="welcome">
			<picture>
				<source srcset={welcome} type="image/webp" />
				<img src={welcomeFallback} alt="Welcome" />
			</picture>
		</span>

		to your new<br />SvelteKit app
	</h1>

	<h2>
		try editing <strong>src/routes/+page.svelte</strong>
	</h2>

	<Counter />
</section>

<style>
	section {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		flex: 0.6;
	}

	h1 {
		width: 100%;
	}

	.welcome {
		display: block;
		position: relative;
		width: 100%;
		height: 0;
		padding: 0 0 calc(100% * 495 / 2048) 0;
	}

	.welcome img {
		position: absolute;
		width: 100%;
		height: 100%;
		top: 0;
		display: block;
	}
</style> -->

<script> 
	import { onMount } from 'svelte';
	import * as Tone from "tone";
	
	let synth = null;
	const pads = Array(16).fill(0);
	var note = null;
	let array = [];

	onMount(() => {	
		console.log("Component mounted");
		synth = new Tone.Synth().toDestination();
		note = "A2";
		array = ["C2", "D2", "E2", "F2", "G2", "A2", "B2", "C3", "D3", "E3", "F3", "G3", "A3", "B3", "C4", "D4"];

	});
	
	async function play(index) {
		await Tone.start();
		console.log("Audio is ready");

		if (!synth) return;
			
		synth.triggerAttackRelease(array[index], "8n");
		console.log("Played note: " + array[index]);
	}


</script>
<div class="layout">
	<div class="left">
		<div class="lefttop">
			<h2>Drum Machine</h2>
		</div>
		<div class="leftmid">
			<h2>Drum Machine</h2>
		</div>
		<div class="leftbottom">
			<h2>Drum Machine</h2>
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
		grid-template-rows: 1fr, 1fr, 1fr;
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


