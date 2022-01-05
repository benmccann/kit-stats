<script context="module" lang="ts">
	import type { Load } from '@sveltejs/kit';

	export const load: Load = async ({ fetch }) => {
		const res = await fetch('https://api.npmjs.org/downloads/range/2000-01-01:2030-01-01/@sveltejs/kit');
		const json = await res.json();
		let total = 0;
		const data = json.downloads.map(e => {
			total += e.downloads
			return { x: e.day, y: total };
		});
		return {
			props: {
				data
			}
		};
	}
</script>

<script lang="ts">
	import DownloadsChart from '$lib/DownloadsChart.svelte';
	export let data = [];
</script>

<svelte:head>
	<title>SvelteKit Downloads</title>
</svelte:head>

<div id="wrapper" class="container mx-auto mt-12 text-center" style="max-width: 900px">

	<h1>SvelteKit has been downloaded nearly 1 million times</h1>

	<div style="position:relative; height:250px">
		<DownloadsChart data={data} />
	</div>
</div>

<style lang="postcss">
	#wrapper {
		font-family: 'Roboto';
		@apply font-light;
	}

	h1 {
		@apply text-4xl;
		@apply font-black;
		@apply mt-4;
		@apply mb-3;
	}
</style>
