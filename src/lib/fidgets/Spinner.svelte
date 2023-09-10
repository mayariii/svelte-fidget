<script lang="ts">
	import Spinner from '$lib/assets/spinner.png';
	import Spinner2 from '$lib/assets/spinner2.png';
	import Spinner3 from '$lib/assets/spinner3.png';
	import Spinner4 from '$lib/assets/spinner4.png';
	import Spinner5 from '$lib/assets/spinner5.png';
	import Spinner6 from '$lib/assets/spinner6.png';
	import Spinner7 from '$lib/assets/spinner7.png';
	import Spinner8 from '$lib/assets/spinner8.png';
	import Spinner9 from '$lib/assets/spinner9.png';

	let spinners = [Spinner, Spinner2, Spinner3, Spinner4, Spinner5,Spinner6, Spinner7, Spinner8, Spinner9];

	let currentSpinner = Spinner;

	let angle = 0,
		autoSpinAngle = 0;

	let isDragging = false,
		isAutoSpinning = false,
		isDragSpinning = false;

	let spinInterval: number | undefined, decelerationTimeout: number | undefined;

	let startX = 0,
		currentAngle = 0,
		lastAngle = 0,
		spinSpeed = 5,
		autoSpinSpeed = 10;

	const INTERVAL_REFRESH_RATE = 16; // approx 60fps

	$: isCurrentlySpinning = isDragSpinning || isAutoSpinning;
	$: delta = currentAngle - lastAngle;
	$: spinSpeed = delta;
	$: angle += delta;
	$: lastAngle = currentAngle;

	function toggleAutoSpin() {
		// stop any ongoing spins
		clearInterval(spinInterval);
		clearTimeout(decelerationTimeout);

		if (isDragSpinning) {
			autoSpinAngle = angle;
			isDragSpinning = false;
			spinSpeed = 0;
		}

		// if not currently spinning, start auto-spinning
		if (!isCurrentlySpinning) {
			// angle updates at every interval to make it spin
			spinInterval = setInterval(() => {
				autoSpinAngle += autoSpinSpeed;
				angle = autoSpinAngle; // sync back to autoSpinAngle in case it gets stopped
			}, INTERVAL_REFRESH_RATE);
			isAutoSpinning = true;
		} else {
			isAutoSpinning = false;
			autoSpinAngle = angle; // sync back to angle so that it stays where it stopped
		}
	}

	function handleMouseDown(event: MouseEvent | TouchEvent) {
		// start drag
		isDragging = true;
		event.preventDefault();
		clearInterval(spinInterval);
		isAutoSpinning = false;

		const clientX = event instanceof MouseEvent ? event.clientX : event.touches[0].clientX;

		startX = clientX - currentAngle;
		isDragSpinning = true;

		document.addEventListener('mousemove', handleMouseMove);
		document.addEventListener('mouseup', handleMouseUp);
		document.addEventListener('touchmove', handleMouseMove);
		document.addEventListener('touchend', handleMouseUp);
	}

	function handleMouseMove(event: MouseEvent | TouchEvent) {
		if (!isDragging) return;

		// update motion
		const clientX = event instanceof MouseEvent ? event.clientX : event.touches[0].clientX;
		const newCurrentAngle = clientX - startX;
		const scalingFactor = 0.2;

		let delta = (newCurrentAngle - lastAngle) * scalingFactor;
		spinSpeed = delta;
		angle += delta;
		lastAngle = newCurrentAngle;
		isDragSpinning = true;
	}

	function handleMouseUp() {
		// stop drag
		isDragging = false;
		document.removeEventListener('mousemove', handleMouseMove);
		document.removeEventListener('mouseup', handleMouseUp);

		// only decelerate if in drag spin mode
		if (!isAutoSpinning) {
			clearTimeout(decelerationTimeout);
			decelerate();
		}
		autoSpinAngle = angle;
	}

	function decelerate() {
		// set threshold for stopping the spinner
		if (Math.abs(spinSpeed) <= 0.03) {
			isDragSpinning = false;
			spinSpeed = 0;
			clearTimeout(decelerationTimeout!);
			autoSpinAngle = angle;
			return;
		}

		// update angle to rotate the spinner
		angle += spinSpeed;

		// decayFactor applied to spinSpeed to gradually slow it down
		const decayFactor = 0.7 + 0.3 * Math.exp(-Math.abs(spinSpeed) / 2);
		spinSpeed *= decayFactor;

		decelerationTimeout = setTimeout(() => {
			decelerate();
		}, INTERVAL_REFRESH_RATE);
	}
</script>



<button
	class="w-[150px] h-[150px] transition-transform duration-200 ease-linear cursor-[grab]"
	style={`transform: rotate(${isAutoSpinning ? autoSpinAngle : angle}deg); cursor: ${
		isDragging ? 'grabbing' : 'grab'
	}`}
	on:mousedown={handleMouseDown}
	on:touchstart={handleMouseDown}
>
	<img src={currentSpinner} class="w-full h-full" alt="spinner" />
</button>

<button on:click={toggleAutoSpin} class="bg-violet-400 bg-opacity-30 px-3 py-1 rounded-full">
	{isCurrentlySpinning ? 'stop' : 'start'} spinning
</button>
<div class="flex flex-col gap-2 items-center">
<p class="text-sm text-gray-300/80">all the spinners!! try them out :)</p>
<div class="grid grid-cols-3 gap-2">
	{#each spinners as spinner}
		<button class="bg-gray-800 p-2 rounded-full" on:click={() => currentSpinner = spinner}>
			<img src={spinner} alt="Choose spinner" class="w-10 h-10" />
		</button>
	{/each}
</div>
</div>