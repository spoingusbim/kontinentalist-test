<script lang="ts">
	import { Tooltip } from 'bits-ui';
	import Slice, { type ChartData } from './slice.svelte';

	export type ChartProps = {
		hiDisplayRange: [number, number];
		prepRange: number[];
		hiBandSteps: number;
		data: ChartData[];
	};

	const { data, hiDisplayRange, hiBandSteps, prepRange }: ChartProps = $props();

	// get angle of slice
	const angle = $derived.by(() => {
		if (data.length === 0) {
			return 0;
		} else {
			return 360 / data.length;
		}
	});

	// get highest precipitation actual data
	const highestPrep = $derived(
		data.reduce<number>((p, c) => {
			if (c.annual_prep > p) {
				return c.annual_prep;
			} else {
				return p;
			}
		}, 0)
	);

	// get highest heat index actual data
	const highestHi = $derived(
		data.reduce<number>((p, c) => {
			if (c.annual_hi > p) {
				return c.annual_hi;
			} else {
				return p;
			}
		}, 0)
	);

	// get highest temperature actual data
	const highestTemp = $derived(
		data.reduce<number>((p, c) => {
			if (c.annual_temp > p) {
				return c.annual_temp;
			} else {
				return p;
			}
		}, 0)
	);

	// from lowest of range to highest actual value for better contrast
	const hiRange: [number, number] = $derived([hiDisplayRange[0], highestHi]);

	// from lowest of range to highest actual value for better contrast
	const tempRange: [number, number] = $derived([hiDisplayRange[0], highestTemp]);

	// old: used lowest of actual value, generates very "cold" results which may look inaccurate
	// const tempRange = $derived(
	// 	data.reduce<[number, number]>(
	// 		(p, c) => {
	// 			if (p[0] === 0 || p[1] === 0) {
	// 				if (p[0] === 0) {
	// 					p[0] = c.annual_temp;
	// 				}
	// 				if (p[1] === 0) {
	// 					p[1] = c.annual_temp;
	// 				}
	// 			} else {
	// 				if (c.annual_temp < p[0]) {
	// 					p[0] = c.annual_temp;
	// 				} else if (c.annual_temp > p[1]) {
	// 					p[1] = c.annual_temp;
	// 				}
	// 			}

	// 			return p;
	// 		},
	// 		[0, 0]
	// 	)
	// );
</script>

<Tooltip.Provider>
	<section>
		<h1 class="title">Abu Dhabi</h1>
		<ul>
			{#each data as d, i (d.year)}
				<Slice
					{prepRange}
					{highestPrep}
					{hiRange}
					{hiDisplayRange}
					{tempRange}
					data={d}
					angle={angle * i}
					angleIncrement={angle}
				/>
			{/each}
		</ul>

		<!-- Below section renders the bands and labels.
		 The circular path math: M cx, (cy - r) a r,r 0 1,1 0,(2*r) a r,r 0 1,1 0,-(2*r) -->
		<svg
			aria-hidden="true"
			class="headers prep-header"
			viewBox="0 0 768 768"
			width="100%"
			height="100%"
		>
			<defs>
				<path
					id="circlePathPrep"
					d="M 384, 39 
            a 345,345 0 1,1 0,690 
            a 345,345 0 1,1 0,-690"
				/>
			</defs>

			<text>
				<textPath href="#circlePathPrep" startOffset="50%" text-anchor="middle">
					Yearly precipitation in mm
				</textPath>
			</text>
		</svg>
		<svg
			aria-hidden="true"
			class="headers hi-header"
			viewBox="0 0 768 768"
			width="100%"
			height="100%"
		>
			<defs>
				<path
					id="circlePathHi"
					d="M 384, 104
            a 280,280 0 1,1 0,560 
            a 280,280 0 1,1 0,-560"
				/>
			</defs>

			<text>
				<textPath href="#circlePathHi" startOffset="50%" text-anchor="middle">
					Average annual heat index
				</textPath>
			</text>
		</svg>
		<svg
			aria-hidden="true"
			class="headers hi-header"
			viewBox="0 0 768 768"
			width="100%"
			height="100%"
		>
			<defs>
				<path
					id="circlePathTemp"
					d="M 384, 234
            a 150,150 0 1,1 0,300 
            a 150,150 0 1,1 0,-300"
				/>
			</defs>

			<text>
				<textPath href="#circlePathTemp" startOffset="50%" text-anchor="middle">
					Average annual temperature
				</textPath>
			</text>
		</svg>
		<div aria-hidden="true" data-highest-prep={highestPrep} class="band prep-circle"></div>
		<div
			aria-hidden="true"
			data-highest-prep={highestPrep}
			data-lowest-hi={hiRange[0]}
			data-highest-hi={hiRange[1]}
			class="band-group hi-circle-group"
		>
			<div class="band hi-circle-highest">
				<span>
					{hiDisplayRange[1]}°C
				</span>
			</div>
			{#each new Array(hiBandSteps).fill(0) as _b, i (`${_b}_${i}`)}
				{@const increment = (hiDisplayRange[1] - hiDisplayRange[0]) / (1 + hiBandSteps)}
				<div data-steps={hiBandSteps + 1} data-index={i + 1} class="band hi-circle-mid">
					<span>
						{hiDisplayRange[0] + increment * (i + 1)}°C
					</span>
				</div>
			{/each}
			<div class="band hi-circle-lowest">
				<span>
					{hiDisplayRange[0]}°C
				</span>
			</div>
		</div>
	</section>
</Tooltip.Provider>

<style>
	h1.title {
		z-index: 10;
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		text-transform: uppercase;
		font-weight: 700;
		font-size: 2.34375vw;
	}
	section {
		position: relative;
	}
	ul {
		position: relative;
		aspect-ratio: 1 / 1;
		height: auto;
		width: 100%;
	}

	svg.headers {
		width: 100%;
		height: 100%;
		position: absolute;
		top: 0;
		left: 0;
		transform: rotate(180deg);
		z-index: 0;
	}

	svg.headers text {
		font-size: 10px;
	}

	div.band-group,
	div.band {
		aspect-ratio: 1 / 1;
		height: auto;
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
	}
	div.band {
		border-radius: 50%;
		border: 1px solid #9c9c9c;
	}

	div.band.prep-circle {
		/* need to * 2 everything as it calculates on both sides */
		width: calc(
			100% - calc(1.5 * 2 * 1.3020833333vw) - 6vw -
				calc(attr(data-highest-prep vw) / var(--prep-scale))
		);
	}

	div.band-group.hi-circle-group {
		width: calc(
			100% - calc(1.5 * 2 * 1.3020833333vw) - 6vw -
				calc(attr(data-highest-prep vw) / var(--prep-scale)) - 15vw
		);
	}

	div.band.hi-circle-highest {
		width: 100%;
	}
	div.band.hi-circle-mid {
		width: calc(
			calc(10vw * var(--hi-scale) / attr(data-steps number) * attr(data-index number)) +
				calc(100% - calc(10vw * var(--hi-scale)))
		);
	}
	div.band.hi-circle-lowest {
		width: calc(100% - calc(10vw * var(--hi-scale)));
	}

	div.band span {
		font-size: 1.3020833333vw;
		position: absolute;
	}
	div.band.hi-circle-highest span,
	div.band.hi-circle-mid span {
		bottom: 100%;
		left: 50%;
		transform: translateX(-50%);
	}
	div.band.hi-circle-lowest span {
		top: 0%;
		left: 50%;
		transform: translateX(-50%);
	}
</style>
