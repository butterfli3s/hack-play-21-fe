<script>
	import { Map as WorldMap, TileLayer, Marker, Popup } from 'sveaflet';
	const latLngs = [
		[53.4505555556, 14.5591666667],
		[50.2687553207, 19.1818316409],
		[50.1303, 19.4381],
		[53.13444444, 17.92166667],
		[50.8422222222, 16.2919444444],
		[54.5222222222, 18.5425]
	];
</script>

<div style="width:100%;height:500px;">
	<WorldMap options={{ center: [51.505, 14.5591666667], zoom: 6 }}>
		<TileLayer url={'https://tile.openstreetmap.org/{z}/{x}/{y}.png'} />
		{#each latLngs as latLng}
			<Marker {latLng} />
		{/each}
	</WorldMap>
</div>
