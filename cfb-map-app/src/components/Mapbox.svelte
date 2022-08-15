<script lang="ts">
	import mapboxgl from "mapbox-gl";
	import { onMount } from "svelte";

	mapboxgl.accessToken = "pk.eyJ1IjoibWFtdW4tYWkiLCJhIjoiY2t6a3h4N3QzMjk0ejJ3bnJ3emp5enluMCJ9.rUcH3jh1RVgO5NRw0MeEXA";

	let [norway_lat, norway_lng] = [60, 8];

	onMount(() => {
		const map = new mapboxgl.Map({
			container: "map", // container ID
			style: "mapbox://styles/mapbox/streets-v11", // style URL
			center: [norway_lng, norway_lat], // starting position [lng, lat]
			zoom: 5, // starting zoom
		});

		const marker = new mapboxgl.Marker()
			.setLngLat([norway_lng, norway_lat])
			.addTo(map);

		const width = document.getElementById("map")?.offsetWidth;
		const height = document.getElementById("map")?.offsetHeight;

		const nav = new mapboxgl.NavigationControl();
		map.addControl(nav, "top-left");

		map.on("load", () => {
			console.log("map loaded...");

			const bounds = map.getBounds();
			console.log({ bounds });
		});

		map.on('wheel',()=>{
			const bounds = map.getBounds();
			const [ne,sw] = [bounds.getNorthEast(), bounds.getSouthEast()];

			console.log('ne',ne)
			console.log('sw',sw)
		})

		map.on("click", (e)=> {
			console.log('lnglat===',e.lngLat)
		});

	});
</script>


<div class="absolute top-0 bottom-0 w-full h-full">
	<div class="menu">
		<span class="title">MapBox Example</span>
	</div>
	<div id="map" />
</div>

<!-- <style>
	#map {
		@apply w-full h-full;
	}
	.menu{
		@apply absolute top-2 right-3 p-3 bg-white z-10;
	}
	.menu .title{
		@apply text-lg;
	}
</style> -->