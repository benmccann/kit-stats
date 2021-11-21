<script>
	import {onMount, afterUpdate, onDestroy} from 'svelte';
	import {Chart, LinearScale, LineController, LineElement, PointElement, TimeSeriesScale, Title, Tooltip} from 'chart.js/dist/chart.esm';
	import 'chartjs-adapter-luxon';
	Chart.register(LinearScale, LineController, LineElement, PointElement, TimeSeriesScale, Title, Tooltip);

	//  Expected data
	export let data = [];    
	export let type = 'line';
	export let options = {
		elements: {
			point: {
				radius: 0
			}
		},
		maintainAspectRatio: false,
		plugins: {
			tooltip: {
				mode: 'nearest',
				intersect: false
			}
		},
		scales: {
			x: {
				type: 'timeseries',
				grid: {
					display: false
				},
				ticks: {
					autoSkipPadding: 75,
					major: {
						enabled: true
					},
					maxRotation: 0,
					sampleSize: 1
				}
			},
			y: {
				grid: {
					display: false,
				},
				ticks: {
					autoSkipPadding: 25
				}
			}
		}
	};

	const chartData = {
		labels: [],
		datasets: [
			{
				data
			}
		]
	};

	let chart = null;
	let chartRef;
	onMount(() => {
		chart = new Chart(chartRef, {
			type,
			data,
			options
		});
	});
	afterUpdate(() => {
		if (!chart) return;
		chart.data = chartData;
		chart.type = type;
		chart.options = options;
		chart.update()
	});
	onDestroy(() => {
		chart = null;
	});
</script>

<canvas bind:this={chartRef}></canvas>
