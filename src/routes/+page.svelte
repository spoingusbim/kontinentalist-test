<script>
	import Chart from '$lib/components/chart.svelte';
	import data from '../lib/assets/data.json';
</script>

<main>
	<Chart {data} />
</main>

<style>
	main {
		padding: 20px;
	}
</style>
