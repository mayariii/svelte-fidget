<script lang="ts">
	import { onMount } from 'svelte';
	import { flip } from 'svelte/animate';
	import { scale } from 'svelte/transition';

	const initialState = Array.from({ length: 16 }, (_, i) => ({ id: i, popped: false }));
	let popSound: HTMLAudioElement;
	let bubbles = initialState;

	$: if (bubbles.every((bubble) => bubble.popped)) {
		bubbles = Array.from({ length: 16 }, (_, i) => ({ id: i, popped: false }));
	}

	onMount(() => {
		popSound = new Audio('/pop.mp3');
	});
</script>

<div class="grid grid-cols-4 gap-4">
	{#each bubbles as bubble (bubble.id)}
		<button
			class="flex items-center justify-center w-16 h-16 rounded-full bg-blue-300 hover:bg-blue-200 focus:outline-none focus:ring focus:ring-blue-400 transition"
			class:opacity-50={bubble.popped}
			animate:flip={{ duration: 500 }}
			on:click={() => {
				if (!bubble.popped) popSound.play();
				bubble.popped = true;
			}}
			disabled={bubble.popped}
		>
			{#if bubble.popped}
				<div in:scale={{ duration: 300 }} class="text-xl bg-blue-400 w-14 h-14 rounded-full" />
			{/if}
		</button>
	{/each}
</div>
