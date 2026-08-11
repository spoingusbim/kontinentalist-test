<script>
	import CardGrid from '$lib/components/card-grid.svelte';
	import Chart from '$lib/components/chart.svelte';
	import datavisData from '../lib/assets/datavis.json';
	import cardData from '../lib/assets/dummy-data.js';
</script>

<main>
	<Chart data={datavisData} />
	<CardGrid data={cardData} />
</main>

<style>
	main {
		padding: 20px;
		display: flex;
		flex-direction: column;
		gap: 2.5rem;
	}
</style>
