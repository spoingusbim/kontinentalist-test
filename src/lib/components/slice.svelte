<script lang="ts">
	import { Tooltip } from 'bits-ui';
	import type { ChartProps } from './chart.svelte';

	type ColumnProps = {
		data: ChartProps['data'][number];
		angle: number;
		highestPrep: number;
		hiRange: [number, number];
		tempRange: [number, number];
		hiRangeDisplay: [number, number];
		angleIncrement: number;
	};
	const {
		data,
		angle,
		angleIncrement,
		highestPrep,
		hiRange,
		tempRange,
		hiRangeDisplay
	}: ColumnProps = $props();

	const bandWidth = $derived((angleIncrement / 360) * 2 * Math.PI * 100);

	const prepClass = $derived.by(() => {
		if (data.annual_prep < 51) {
			return 'prep-100';
		} else if (data.annual_prep > 50 && data.annual_prep < 101) {
			return 'prep-300';
		} else if (data.annual_prep > 100 && data.annual_prep < 151) {
			return 'prep-500';
		} else if (data.annual_prep > 150 && data.annual_prep < 201) {
			return 'prep-700';
		} else if (data.annual_prep > 200) {
			return 'prep-900';
		}
	});

	const hiBands = $derived.by(() => {
		const bandsCount = 7;
		const bandIncrement = (hiRange[1] - hiRange[0]) / bandsCount;
		const bandRange = new Array(bandsCount + 1).fill(0).map((_, i) => {
			return hiRange[0] + i * bandIncrement;
		});
		return bandRange;
	});
	const tempBands = $derived.by(() => {
		const bandsCount = 7;
		const bandIncrement = (tempRange[1] - tempRange[0]) / bandsCount;
		const bandRange = new Array(bandsCount + 1).fill(0).map((_, i) => {
			return hiRange[0] + i * bandIncrement;
		});
		return bandRange;
	});

	const hiClass = $derived.by(() => {
		const index = hiBands.findIndex((b, i, arr) => {
			if (i === arr.length - 1) {
				return data.annual_hi >= b;
			} else {
				return data.annual_hi >= b && data.annual_hi < arr[i + 1];
			}
		});
		return `hi-${index + 1}00`;
	});
	const tempClass = $derived.by(() => {
		const index = tempBands.findIndex((b, i, arr) => {
			if (i === arr.length - 1) {
				return data.annual_temp >= b;
			} else {
				return data.annual_temp >= b && data.annual_temp < arr[i + 1];
			}
		});
		return `temp-${index + 1}00`;
	});

	const prepTether = Tooltip.createTether<{
		label: string;
		description: string;
		shortcut: string;
	}>();
	const hiTether = Tooltip.createTether<{
		label: string;
		description: string;
		shortcut: string;
	}>();
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
<li data-angle={angle}>
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
				class="h-full w-full cursor-pointer rounded-[50%] border-black hover:border"
				tether={hiTether}
			>
				<div
					data-lowest-hi={hiRangeDisplay[0]}
					data-highest-hi={hiRangeDisplay[1]}
					data-hi={data.annual_hi}
					class={`hi-column ${hiClass}`}
				></div>
			</Tooltip.Trigger>
		</div>

		<div
			class="temp-vis pointer-events-none"
			aria-label={`Average annual temperature: ${data.annual_temp}`}
		>
			<Tooltip.Trigger
				class="pointer-events-auto z-50 h-[1vw] cursor-pointer rounded-[50%] border-black hover:border"
				tether={tempTether}
			>
				<div
					data-band-width={bandWidth}
					data-temp={data.annual_temp}
					data-angle={angleIncrement / 2}
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
		width: 7px;
		height: 10vw;
		display: flex;
		align-items: end;
		justify-content: end;
		margin-bottom: 5vw;
	}

	li div .hi-vis > * {
		height: 100% !important;
		width: 100% !important;
		appearance: none;
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
		transform: rotate(calc(5 * attr(data-angle deg)));
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
			calc(0.5 * attr(data-band-width %)) 0,
			calc(-0.5 * attr(data-band-width %)) 0
		);
	}
</style>
