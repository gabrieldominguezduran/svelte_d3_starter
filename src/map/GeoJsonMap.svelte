<script>
	import { json, geoAlbersUsa, geoPath, active } from 'd3';
	let width, height;

	const geojsonPath =
		'https://raw.githubusercontent.com/plotly/datasets/master/geojson-counties-fips.json';

	let geojson;

	json(geojsonPath).then((data) => (geojson = data));

	$: projection = geoAlbersUsa().fitSize([width, height], geojson);
	$: pathGenerator = geoPath(projection);

	let counties = [];
	$: if (geojson)
		counties = geojson.features.map((feature) => {
			return {
				...feature,
				path: pathGenerator(feature),
			};
		});

	let hoveredCountyId = null;
</script>

<div
	class="main"
	bind:clientWidth={width}
	bind:clientHeight={height}
	role="presentation"
>
	<svg
		width={width}
		height={height}
	>
		{#each counties as { id, path }}
			<!-- svelte-ignore a11y-no-static-element-interactions -->
			<path
				d={path}
				class:active={hoveredCountyId === id}
				on:mouseenter={() => (hoveredCountyId = id)}
			/>
		{/each}
	</svg>
</div>

<style>
	.main {
		width: 100vw;
		height: 100vh;
		overflow: hidden;
	}

	path {
		fill: purple;
		stroke: none;
		opacity: 0.5;
		transition: opacity 0.4s ease-in-out;
		transition-delay: 0.8s;
	}

	path.active {
		opacity: 1;
		transition-duration: 0s;
		transition-delay: 0s;
	}
</style>
