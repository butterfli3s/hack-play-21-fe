<script>
	import { onMount } from 'svelte';
	import { Map as WorldMap, TileLayer, Marker, Popup } from 'sveaflet';
	import Form from './Form.svelte';
	import L from 'leaflet';

	let cells = $state([]);

	async function loadCells() {
		try {
			const response = await fetch('/hackplay_cells.json');
			cells = await response.json();

			// Initialize alert property (so it's always defined)
			for (const c of cells) c.alert = false
		} catch (err) {
			console.error('Error loading hackplay_cells.json:', err);
		}
	}

	function connectWebSocket() {
		const ws = new WebSocket(`ws://${window.location.host}/api/ws/cells`);

		ws.onmessage = (event) => {
			const data = JSON.parse(event.data);
			if (data.type === 'cell_update') {
				const cell = cells.find((c) => c.cell_rk === data.cell_rk);
				if (cell) {
					cell.alert = data.status === 'alert';
					// Force Svelte to notice the object changed
					cells = cells;
				}
			}
		};

		ws.onclose = () => {
			console.warn('WebSocket closed. Reconnecting in 3s...');
			setTimeout(connectWebSocket, 3000);
		};
	}

	onMount(async () => {
		await loadCells();
		connectWebSocket();
	});
</script>

<div style="width:100%;height:500px;">
	<WorldMap options={{ center: [51.505, 14.559], zoom: 6 }}>
		<TileLayer url={'https://tile.openstreetmap.org/{z}/{x}/{y}.png'} />
		{#each cells as cell (cell.cell_rk)}
			<Marker
				latLng={[cell.cell_lat, cell.cell_lon]}
				options={{
					icon: L.divIcon({
						className: '',
						html: `<div style="background-color:${
							cell.alert ? 'red' : 'green'
						};width:16px;height:16px;border-radius:50%;border:2px solid white;"></div>`,
						iconSize: [16, 16],
					})
				}}
			>
				<Popup>
					<h4>Cell {cell.cell_rk}</h4>
					<p><b>User:</b> {cell.user_id}</p>
					<p><b>Status:</b> {cell.alert ? '⚠️ ALERT' : '✅ Normal'}</p>
				</Popup>
			</Marker>
		{/each}
	</WorldMap>

	<Form />
</div>