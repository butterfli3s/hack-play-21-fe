<script>
	import { enhance } from '$app/forms';
	const user_locations = fetch('./user_locations_hackplay_sample.json')
		.then((response) => response.json())
		.then((result) => [...new Map(result.map((item) => [item.user_id, item])).values()])
		.catch((error) => console.error('Error fetching JSON:', error));
	const selected_user = $state({ user_id: '', cell_rk: 0, start_dttm: '' });
	const update_user = () =>
		fetch('./user_locations_hackplay_sample.json')
			.then((response) => response.json())
			.then((users) => {
				const user = users.filter((data) => selected_user.user_id === data.user_id)[0];
				selected_user.cell_rk = user.cell_rk;
				selected_user.start_dttm = user.start_dttm;
			})
			.catch((error) => console.error('Error fetching JSON:', error));
</script>

{#await user_locations}
	<p>Retreiving the user locations...</p>
{:then users}
	<form
		onsubmit={(event) => {
			event.preventDefault();
			console.log('User ID ' + event.target[0].value);
			console.log('Cell ID ' + event.target[1].value);
			console.log('Timestamp ' + event.target[2].value);
		}}
	>
		<label for="user_id">User ID:</label><br />
		<select name="user_id" bind:value={selected_user.user_id} onchange={update_user}>
			{#each users as user}
				<option value={user.user_id}>{user.user_id}</option>
			{/each}
		</select>
		<br />
		<label for="cell_rk">Inferred cell ID:</label><br />
		<input type="text" id="cell_rk" name="cell_rk" value={selected_user.cell_rk} />
		<br />
		<label for="start_dttm">Inferred date:</label><br />
		<input type="text" id="start_dttm" name="start_dttm" value={selected_user.start_dttm} />
		<br />
		<button type="submit">Submit</button>
	</form>
{:catch error}
	<p>Error: {error}</p>
{/await}
