<script lang="ts">
	import Card, { type CardProps } from './card.svelte';

	type Props = {
		data: CardProps[];
	};
	const { data }: Props = $props();
</script>

<section class="flex w-full">
	<div class="mx-auto w-full max-w-360">
		<h1 class="mb-6 text-center">Click on the cards to learn more about each solution</h1>
		<div class="grid grid-cols-1 gap-5 min-[480px]:grid-cols-2 md:grid-cols-3">
			{#each data as d (d.title)}
				<Card {...d} />
			{/each}
		</div>
	</div>
</section>
