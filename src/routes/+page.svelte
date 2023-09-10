<script>
	import Spinner from './fidgets/Spinner.svelte';
</script>

<div class="h-screen w-screen">
	<div class="flex flex-col gap-8 items-center justify-center h-full text-white">
		<h1 class="text-3xl">svelte fidget</h1>

		<p class="text-violet-300/80 text-sm max-w-[30ch] text-center">
			drag along the x-axis to spin manually or click the button for auto-spin ✨
		</p>

		<Spinner />

		<p class="text-gray-500 text-xs">
			made by <a href="https://mayari.io" class="text-violet-500">mayari.io</a>
		</p>
	</div>
</div>
