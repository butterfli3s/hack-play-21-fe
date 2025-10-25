<script>
	const user_locations = fetch('./user_locations_hackplay_sample.json')
		.then((response) => response.json())
		.then((result) => [...new Map(result.map((item) => [item.user_id, item])).values()])
		.catch((error) => console.error('Error fetching JSON:', error));
	const selected_user = $state({ user_id: '', cell_rk: 0, timestamp: '', message: '' });
	const update_user = () =>
		fetch('./user_locations_hackplay_sample.json')
			.then((response) => response.json())
			.then((users) => {
				const user = users.filter((data) => selected_user.user_id === data.user_id)[0];
				selected_user.cell_id = user.cell_rk;
				selected_user.timestamp = user.start_dttm;
			})
			.catch((error) => console.error('Error fetching JSON:', error));
	let result_msg = $state('');
</script>

{#await user_locations}
	<p>Retreiving the user locations...</p>
{:then users}
	<form
		onsubmit={(event) => {
			event.preventDefault();
			fetch('http://localhost:8000/signal', {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json',
					'Access-Control-Allow-Origin': '*'
				},
				body: JSON.stringify(selected_user)
			})
				.then((response) => response.json())
				.then((json) => {
					result_msg = json.message;
				})
				.catch((error) => console.error('Error:', error));
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
		<input type="text" id="cell_rk" name="cell_rk" bind:value={selected_user.cell_rk} />
		<br />
		<!-- <label for="start_dttm">Inferred date:</label><br />
		<input type="text" id="timestamp" name="timestamp" bind:value={selected_user.timestamp} />
		<br /> -->
		<label for="text">Insert your message:</label><br />
		<input type="text" id="message" name="message" bind:value={selected_user.message} />
		<br />
		<button type="submit">Submit</button>
	</form>
{:catch error}
	<p>Error: {error}</p>
{/await}

<p>Request result: {result_msg}</p>
