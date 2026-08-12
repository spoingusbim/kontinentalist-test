<script lang="ts">
	import { Tooltip } from 'bits-ui';

	export type ChartData = {
		year: number;
		annual_temp: number;
		annual_hi: number;
		annual_prep: number;
		location?: string;
	};

	type ColumnProps = {
		data: Omit<ChartData, 'location'>;
		angle: number;
		highestPrep: number;
		hiRange: [number, number];
		tempRange: [number, number];
		hiDisplayRange: [number, number];
		angleIncrement: number;
		prepRange: number[];
	};

	const {
		prepRange,
		data,
		angle,
		angleIncrement,
		highestPrep,
		hiRange,
		tempRange,
		hiDisplayRange
	}: ColumnProps = $props();

	// angle of each slice / 360 * 2 Pi * R
	const bandWidth = $derived((angleIncrement / 360) * 2 * Math.PI * 35);

	// get precipitation class programatically
	const prepClass = $derived.by(() => {
		const index = prepRange.findIndex((p, i, arr) => {
			if (i === arr.length - 1 && data.annual_prep > p) {
				return true;
			}
			return data.annual_prep > p && data.annual_prep <= arr[i + 1];
		});

		if (index >= 0) {
			return `prep-${index + 1}00`;
		}
	});

	// old hard-coded method to add class to precipitation bubble
	// const prepClass = $derived.by(() => {
	// 	if (data.annual_prep < 51) {
	// 		return 'prep-100';
	// 	} else if (data.annual_prep > 50 && data.annual_prep < 101) {
	// 		return 'prep-300';
	// 	} else if (data.annual_prep > 100 && data.annual_prep < 151) {
	// 		return 'prep-500';
	// 	} else if (data.annual_prep > 150 && data.annual_prep < 201) {
	// 		return 'prep-700';
	// 	} else if (data.annual_prep > 200) {
	// 		return 'prep-900';
	// 	}
	// });

	// get heat index colour steps
	const hiSteps = $derived.by(() => {
		const stepCount = 7;
		const stepIncrement = (hiRange[1] - hiRange[0]) / stepCount;
		const stepArray = new Array(stepCount + 1).fill(0).map((_, i) => {
			return hiRange[0] + i * stepIncrement;
		});
		return stepArray;
	});

	// get temp index colour steps
	const tempSteps = $derived.by(() => {
		const stepCount = 7;
		const stepIncrement = (tempRange[1] - tempRange[0]) / stepCount;
		const stepArray = new Array(stepCount + 1).fill(0).map((_, i) => {
			return hiRange[0] + i * stepIncrement;
		});
		return stepArray;
	});

	// get heat index class programatically
	const hiClass = $derived.by(() => {
		const index = hiSteps.findIndex((b, i, arr) => {
			if (i === arr.length - 1) {
				return data.annual_hi >= b;
			} else {
				return data.annual_hi >= b && data.annual_hi < arr[i + 1];
			}
		});
		return `hi-${index + 1}00`;
	});

	// get temperature class programatically
	const tempClass = $derived.by(() => {
		const index = tempSteps.findIndex((b, i, arr) => {
			if (i === arr.length - 1) {
				return data.annual_temp >= b;
			} else {
				return data.annual_temp >= b && data.annual_temp < arr[i + 1];
			}
		});
		return `temp-${index + 1}00`;
	});

	// for tooltip
	const prepTether = Tooltip.createTether<{
		label: string;
		description: string;
		shortcut: string;
	}>();

	// for tooltip
	const hiTether = Tooltip.createTether<{
		label: string;
		description: string;
		shortcut: string;
	}>();

	// for tooltip
	const tempTether = Tooltip.createTether<{
		label: string;
		description: string;
		shortcut: string;
	}>();
</script>

<Tooltip.Root tether={prepTether} delayDuration={200}>
	<Tooltip.Content sideOffset={8} class="z-50 bg-black/50">
		<div class="flex flex-col p-1.5 text-[1.8229166667vw] text-white">
			<h3>Year: {data.year}</h3>
			<h4>Precipitation: {data.annual_prep}mm</h4>
		</div>
	</Tooltip.Content>
</Tooltip.Root>
<Tooltip.Root tether={hiTether} delayDuration={200}>
	<Tooltip.Content sideOffset={8} class="z-50 bg-black/50">
		<div class="flex flex-col p-1.5 text-[1.8229166667vw] text-white">
			<h3>Year: {data.year}</h3>
			<h4>Heat Index: {data.annual_hi}°C</h4>
		</div>
	</Tooltip.Content>
</Tooltip.Root>
<Tooltip.Root tether={tempTether} delayDuration={200}>
	<Tooltip.Content sideOffset={8} class="z-50 bg-black/50">
		<div class="flex flex-col p-1.5 text-[1.8229166667vw] text-white">
			<h3>Year: {data.year}</h3>
			<h4>Temperature: {data.annual_temp}°C</h4>
		</div>
	</Tooltip.Content>
</Tooltip.Root>

<li tabIndex={0} data-angle={angle}>
	<div data-angle={angle}>
		<h2>{data.year.toString()}</h2>
		<div data-highest-prep={highestPrep} class="prep-vis">
			<Tooltip.Trigger
				class="cursor-pointer rounded-[50%] border-black hover:border"
				tether={prepTether}
			>
				<div
					aria-label={`Yearly precipitation in mm: ${data.annual_prep}`}
					data-prep={data.annual_prep}
					class={`prep-circle ${prepClass}`}
				></div>
			</Tooltip.Trigger>
		</div>
		<div class="hi-vis" aria-label={`Average annual heat index: ${data.annual_hi}`}>
			<Tooltip.Trigger
				class="group flex h-full w-full cursor-pointer items-end justify-end"
				tether={hiTether}
			>
				<div
					data-lowest-hi={hiDisplayRange[0]}
					data-highest-hi={hiDisplayRange[1]}
					data-hi={data.annual_hi}
					class={`hi-column ${hiClass} rounded-[50%] border-black group-hover:border`}
				></div>
			</Tooltip.Trigger>
		</div>
		<div
			class="temp-vis pointer-events-none"
			aria-label={`Average annual temperature: ${data.annual_temp}`}
		>
			<Tooltip.Trigger
				class="pointer-events-auto z-50 h-[1vw] cursor-pointer rounded-[50%] hover:opacity-80"
				tether={tempTether}
			>
				<div
					data-band-width={bandWidth}
					data-temp={data.annual_temp}
					data-angle={angle}
					class={`temp ${tempClass}`}
				></div>
			</Tooltip.Trigger>
		</div>
	</div>
</li>

<style>
	li {
		z-index: 1;
		height: 50%;
		left: 50%;
		top: 50%;
		position: absolute;
		transform: translate(-50%, -100%) rotate(attr(data-angle deg));
		transform-origin: bottom;
	}

	li div {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: start;
	}

	li h2 {
		font-size: 1.3020833333vw;
		margin-bottom: 3vw;
	}
	li div.prep-vis {
		height: auto;
		aspect-ratio: 1 / 1;
		width: calc(attr(data-highest-prep vw) / var(--prep-scale));
		display: flex;
		align-items: center;
		justify-content: center;
		margin-bottom: 5vw;
	}

	li div.prep-vis div.prep-circle {
		height: auto;
		aspect-ratio: 1 / 1;
		border-radius: 50%;
		width: calc(attr(data-prep vw) / var(--prep-scale));
		opacity: 0.9;
	}

	li div .hi-vis {
		width: 0.5vw;
		height: 10vw;
		display: flex;
		align-items: end;
		justify-content: end;
		margin-bottom: 5vw;
	}

	li div .hi-vis .hi-column {
		border-radius: 50%;
		width: 100%;
		opacity: 0.9;
		height: calc(
			(attr(data-hi number) - attr(data-lowest-hi number)) /
				(attr(data-highest-hi number) - attr(data-lowest-hi number)) * 100%
		);
	}
	li div .temp-vis {
		position: absolute;
		top: 100%;
		left: 50%;
		transform: translate(-50%, -50%);
		width: auto;
		border-radius: 50%;
		height: 35vw;
		aspect-ratio: 1 / 1;
	}

	li div .temp {
		transform: rotate(calc(45deg));
		transform-origin: center;
		border-width: 1vw;
		border-style: solid;
		position: absolute;
		top: 0%;
		left: 0%;
		width: 100%;
		border-radius: 50%;
		height: 100%;
		aspect-ratio: 1 / 1;
		clip-path: polygon(
			50% 50%,
			calc(0.5 * attr(data-band-width vw)) 0,
			calc(-0.5 * attr(data-band-width vw)) 0
		);
	}
</style>
