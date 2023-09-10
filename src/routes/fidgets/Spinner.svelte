<script lang="ts">
	import Spinner from '$lib/assets/fidget3.png';

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
		autoSpinSpeed = 15;

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
  

	function handleMouseDown(event: MouseEvent) {
		// start drag
		isDragging = true;
		event.preventDefault();
		clearInterval(spinInterval);
		isAutoSpinning = false;
		startX = event.clientX - currentAngle;
		isDragSpinning = true;

		document.addEventListener('mousemove', handleMouseMove);
		document.addEventListener('mouseup', handleMouseUp);
	}

	function handleMouseMove(event: MouseEvent) {
		if (!isDragging) return;

		// update motion
		const newCurrentAngle = event.clientX - startX;
		let delta = -(newCurrentAngle - lastAngle);
		spinSpeed = delta;
		angle += delta;
		lastAngle = newCurrentAngle;
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
	class="w-[200px] h-[200px] transition-transform duration-200 ease-linear cursor-[grab]"
	style={`transform: rotate(${isAutoSpinning ? autoSpinAngle : angle}deg); cursor: ${
		isDragging ? 'grabbing' : 'grab'
	}`}
	on:mousedown={handleMouseDown}
>
	<img src={Spinner} class="w-full h-full" alt="spinner" />
</button>

<button on:click={toggleAutoSpin} class="bg-violet-400 bg-opacity-30 px-3 py-1 rounded-full">
	{isCurrentlySpinning ? 'stop' : 'start'} spinning
</button>
