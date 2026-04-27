<script lang="ts">
    import { getContext } from 'svelte';
    import pianoImg from '$lib/images/piano.svg';
    import guitarImg from '$lib/images/guitar.svg';
    import fluteImg from '$lib/images/flute.svg';
    import cymbalImg from '$lib/images/cymbal.svg';

export let side: string;

    const instruments = getContext("instruments") as any;
    const { openModal } = getContext("audioContext") as any;

    $: store = instruments[side];

    const instrumentImages = {
        piano: pianoImg,
        guitar: guitarImg,
        flute: fluteImg,
        cymbal: cymbalImg
    };

    function handleClick() {
        openModal(side);
    }
</script>

<button class="instrument-display" on:click={handleClick}>
    <img src={instrumentImages[$store as keyof typeof instrumentImages]} alt={$store} />
    <span class="instrument-name">{$store}</span>
</button>

<style>
    .instrument-display {
        background: linear-gradient(135deg, #2d2d2d 0%, #1a1a1a 100%);
        border: 2px solid #ff3e00;
        border-radius: 12px;
        padding: 15px;
        cursor: pointer;
        transition: all 0.3s ease;
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 10px;
        width: 140px;
        aspect-ratio: 1;
    }

    .instrument-display:hover {
        background: linear-gradient(135deg, #3d3d3d 0%, #2a2a2a 100%);
        border-color: #ff6b00;
        transform: translateY(-4px);
        box-shadow: 0 8px 16px rgba(255, 62, 0, 0.3);
    }

    .instrument-display:active {
        transform: translateY(-2px);
    }

    img {
        width: 80px;
        height: 80px;
        object-fit: contain;
        filter: brightness(1.1);
    }

    .instrument-name {
        color: #fff;
        font-size: 14px;
        font-weight: bold;
        text-transform: uppercase;
        letter-spacing: 1px;
    }
</style>