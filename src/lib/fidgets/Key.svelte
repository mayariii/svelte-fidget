<script lang="ts">
	import { onMount } from 'svelte';

	let clickOnSound: HTMLAudioElement, clickOffSound: HTMLAudioElement;
	let isPressed = false;

	let keys = Array.from({ length: 1 }, (_, i) => ({
		id: `key-${i}`,
		pressed: false
	}));

	onMount(() => {
		clickOnSound = new Audio('/click-on.mp3');
		clickOffSound = new Audio('/click-off.mp3');
	});
</script>

<div class="flex gap-2">
	{#each keys as key (key.id)}
		<button
			class="w-[150px] p-3 bg-gray-700 rounded-lg ease-linear duration-100 focus:outline-none focus:ring focus:ring-gray-600 transition"
			class:scale-95={isPressed}
			on:click={() => {
				isPressed ? clickOffSound.play() : clickOnSound.play();
				isPressed = !isPressed;
			}}
		>
			{#if isPressed}
				<div class="bg-gray-800 p-2 rounded">clack</div>
			{:else}
				<div class="bg-gray-900 p-2 rounded">click</div>
			{/if}
		</button>
	{/each}
</div>
