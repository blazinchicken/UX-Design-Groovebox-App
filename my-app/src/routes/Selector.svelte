<script>
    import { getContext } from 'svelte';
    export let side;

    const instruments = getContext("instruments");

    $: store = instruments[side];
</script>

<select bind:value={$store}>
    <option value="piano">Piano</option>
    <option value="guitar">Guitar</option>
    <option value="flute">Flute</option>
    <option value="cymbal">Cymbal</option>
    <option value="kick">Kick</option>
</select>