<script>
	import { Map as WorldMap, TileLayer, Marker, Popup } from 'sveaflet';
	const cells = fetch('./hackplay_cells.json')
		.then((response) => response.json())
		.catch((error) => console.error('Error fetching JSON:', error));
</script>

<div style="width:100%;height:500px;">
	<WorldMap options={{ center: [51.505, 14.5591666667], zoom: 6 }}>
		<TileLayer url={'https://tile.openstreetmap.org/{z}/{x}/{y}.png'} />
		{#await cells then cellsRes}
			{#each cellsRes as cell}
				<Marker latLng={[cell.cell_lat, cell.cell_lon]} />
			{/each}
		{/await}
	</WorldMap>
</div>
