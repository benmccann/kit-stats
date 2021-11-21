<script>
	import {onMount, afterUpdate, onDestroy} from 'svelte';
	import {Chart, Filler, LinearScale, LineController, LineElement, PointElement, TimeSeriesScale, Title, Tooltip} from 'chart.js/dist/chart.esm';
	import 'chartjs-adapter-luxon';
	Chart.register(Filler, LinearScale, LineController, LineElement, PointElement, TimeSeriesScale, Title, Tooltip);

	Chart.defaults.font.family = 'Roboto';
	Chart.defaults.font.weight = 'bold';

	//  Expected data
	export let data = [];    
	export let type = 'line';

	const totalDuration = 5000;
	const delayBetweenPoints = totalDuration / data.length;
	const previousY = (ctx) => ctx.index === 0 ? ctx.chart.scales.y.getPixelForValue(100) : ctx.chart.getDatasetMeta(ctx.datasetIndex).data[ctx.index - 1].getProps(['y'], true).y;
	const animation = {
		x: {
			type: 'number',
			easing: 'linear',
			duration: delayBetweenPoints,
			from: NaN, // the point is initially skipped
			delay(ctx) {
				if (ctx.type !== 'data' || ctx.xStarted) {
					return 0;
				}
				ctx.xStarted = true;
				return ctx.index * delayBetweenPoints;
			}
		},
		y: {
			type: 'number',
			easing: 'linear',
			duration: delayBetweenPoints,
			from: previousY,
			delay(ctx) {
				if (ctx.type !== 'data' || ctx.yStarted) {
					return 0;
				}
				ctx.yStarted = true;
				return ctx.index * delayBetweenPoints;
			}
		}
	};

	export let options = {
		animation,
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
				data,
				backgroundColor: '#f8dddd',
				borderColor: '#cf1111',
				fill: true
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
