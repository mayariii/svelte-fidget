<script>
	import Popper from '$lib/fidgets/Popper.svelte';
</script>

<div class="h-screen w-screen">
	<div class="flex flex-col gap-8 items-center justify-center h-full text-white">
		<a href="/" class="text-3xl">svelte fidget</a>

		<div>
			<p class="text-violet-300/80 text-sm max-w-[35ch] text-center">
				pop to your heart's content 🫧
			</p>
			<p class="text-gray-400/80 text-xs text-center">(has sound)</p>
		</div>
		<Popper />

		<p class="text-gray-500 text-xs">
			made by <a href="https://mayari.io" target="_blank" class="text-violet-500">mayari.io</a>
		</p>
	</div>
</div>
