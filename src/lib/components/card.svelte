<script lang="ts">
	export type CardProps = {
		title: string;
		carousel_title: string;
		bg: string;
		content: {
			id: string;
			action: string;
			desc: string;
			bg: string;
		}[];
	};
	import { Dialog } from 'bits-ui';
	import { fade } from 'svelte/transition';
	const { title, carousel_title, bg, content }: CardProps = $props();

	let slide = $state(0);
</script>

<Dialog.Root>
	<Dialog.Trigger
		style={`background-image: url('${bg}')`}
		class="aspect-square h-auto w-full cursor-pointer bg-cover bg-no-repeat transition-all hover:opacity-80"
	>
		<div class="sr-only">{title}</div>
	</Dialog.Trigger>
	<Dialog.Portal>
		<Dialog.Overlay forceMount class="fixed inset-0 z-50 bg-black/30">
			{#snippet child({ props, open })}
				{#if open}
					<div {...props} transition:fade></div>
				{/if}
			{/snippet}
		</Dialog.Overlay>
		<Dialog.Content
			forceMount
			class="fixed top-1/2 left-1/2 z-60 flex w-full max-w-120 -translate-x-1/2 -translate-y-1/2 items-center justify-center p-5 md:max-w-210"
		>
			{#snippet child({ props, open })}
				{#if open}
					<div {...props} transition:fade>
						<div class="relative grid w-full grid-cols-1 bg-white md:max-w-210 md:grid-cols-3">
							<div
								style={`background-image: url('${content[slide].bg}')`}
								class="aspect-square h-full w-full bg-cover bg-no-repeat"
							></div>
							<div class="col-span-2 flex h-auto w-full flex-col p-5">
								<Dialog.Title
									level={1}
									class="mb-1 text-sm font-semibold tracking-tight uppercase min-[480px]:text-base"
								>
									{carousel_title}
								</Dialog.Title>
								<Dialog.Title level={2} class="mb-4 text-xl font-semibold min-[480px]:text-2xl">
									{content[slide].action}
								</Dialog.Title>
								<Dialog.Description class="mb-5 text-base min-[480px]:text-lg">
									{content[slide].desc}
								</Dialog.Description>
								<div
									class="controls mt-auto flex w-full items-center justify-between text-sm min-[480px]:text-base"
								>
									<button
										class="cursor-pointer transition-all hover:opacity-60"
										onclick={() => {
											if (slide === 0) {
												slide = content.length - 1;
											} else {
												slide = slide - 1;
											}
										}}
										><svg
											aria-hidden="true"
											xmlns="http://www.w3.org/2000/svg"
											width="24"
											height="24"
											viewBox="0 0 24 24"
										>
											<path d="M0 0h24v24H0z" fill="none" />
											<path
												fill="currentColor"
												d="m10.8 12l3.9 3.9q.275.275.275.7t-.275.7t-.7.275t-.7-.275l-4.6-4.6q-.15-.15-.212-.325T8.425 12t.063-.375t.212-.325l4.6-4.6q.275-.275.7-.275t.7.275t.275.7t-.275.7z"
											/>
										</svg>
										<span class="sr-only">Previous</span></button
									>
									<span class="text-sm">{slide + 1} of {content.length}</span>
									<button
										class="cursor-pointer transition-all hover:opacity-60"
										onclick={() => {
											if (slide === content.length - 1) {
												slide = 0;
											} else {
												slide = slide + 1;
											}
										}}
										><svg
											aria-hidden="true"
											xmlns="http://www.w3.org/2000/svg"
											width="24"
											height="24"
											viewBox="0 0 24 24"
										>
											<path d="M0 0h24v24H0z" fill="none" />
											<path
												fill="currentColor"
												d="M12.6 12L8.7 8.1q-.275-.275-.275-.7t.275-.7t.7-.275t.7.275l4.6 4.6q.15.15.213.325t.062.375t-.062.375t-.213.325l-4.6 4.6q-.275.275-.7.275t-.7-.275t-.275-.7t.275-.7z"
											/>
										</svg>

										<span class="sr-only">Next</span></button
									>
								</div>
							</div>
							<Dialog.Close
								class="absolute top-5 right-5 cursor-pointer transition-all hover:opacity-60"
								><svg
									aria-hidden="true"
									xmlns="http://www.w3.org/2000/svg"
									width="24px"
									height="24px"
									viewBox="0 0 24 24"
								>
									<path d="M0 0h24v24H0z" fill="none" />
									<path
										fill="currentColor"
										d="m12 13.4l-4.9 4.9q-.275.275-.7.275t-.7-.275t-.275-.7t.275-.7l4.9-4.9l-4.9-4.9q-.275-.275-.275-.7t.275-.7t.7-.275t.7.275l4.9 4.9l4.9-4.9q.275-.275.7-.275t.7.275t.275.7t-.275.7L13.4 12l4.9 4.9q.275.275.275.7t-.275.7t-.7.275t-.7-.275z"
									/>
								</svg><span class="sr-only">Close</span></Dialog.Close
							>
						</div>
					</div>
				{/if}
			{/snippet}
		</Dialog.Content>
	</Dialog.Portal>
</Dialog.Root>
