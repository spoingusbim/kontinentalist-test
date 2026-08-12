<script lang="ts">
	import CardGrid from '$lib/components/card-grid.svelte';
	import Chart from '$lib/components/chart.svelte';
	import datavisData from '../lib/assets/datavis.json';
	import cardData from '../lib/assets/dummy-data.js';

	/* for the heat index bands, [lowest, highest] */
	const hiDisplayRange: [number, number] = [25, 35];

	/* number of steps within the heat index range */
	const hiBandSteps: number = 1;

	/* precipitation range values, must correspond to number of colours and named classes available in css */
	const prepRange = [0, 50, 100, 150, 200];
</script>

<main>
	<Chart data={datavisData} {hiDisplayRange} {hiBandSteps} {prepRange} />
	<CardGrid data={cardData} />
</main>

<style>
	main {
		overflow: hidden;
		padding: 20px;
		display: flex;
		flex-direction: column;
		gap: 2.5rem;
	}
</style>
