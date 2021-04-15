<script lang="ts">
	//TODO: Add transitions
	//TODO: Add "book me on Metafy! or just donate."

	let files: FileList;
	let text: string;
	$: promise = files ? files[0].text().then((_text) => (text = _text)) : new Promise(() => {});
	$: columns = text ? text.split('\r\n')[0].split(',') : [];
	$: data = columns ? columns.reduce((acc, curr) => ((acc[curr] = ''), acc), {}) : [];

	let name: string;
	let calculations: string[] = [];
	let minimum: number;
	let maximum: number;
</script>

<svelte:head>
	<title>framekiller</title>
</svelte:head>

<h1>framekiller</h1>
<h2>
	find automatic timings for safejumps, meaties, and other setups; given a set of character
	frame-data (CSV, Excel Sheet, or Google Sheet).
</h2>

<p>upload frame-data:</p>
<small>(or select a google drive file)</small>

<input type="file" bind:files />

{#await promise then resolved}
	{#if resolved}
		{#if columns}
			<p>select the name column:</p>
			{#each columns as column}
				<label>
					<input type="radio" bind:group={name} value={column} />
					<span>{column}</span>
				</label>
			{/each}
			{#if name}
				<p>select the calculation column(s):</p>
				{#each columns as column}
					{#if column != name}
						<label>
							<input type="checkbox" bind:group={calculations} value={column} />
							<span>{column}</span>
						</label>
					{/if}
				{/each}
				{#if calculations.length}
					<p>set the frame-search range:</p>
					<label>
						<input type="number" min="1" bind:value={minimum} />
						<span>starting at frame {minimum || 'x'}.</span>
					</label>
					{#if minimum}
						<label>
							<input type="number" min="1" bind:value={maximum} />
							<span>ending on frame {maximum || 'x'}.</span>
						</label>
						{#if maximum}
							<p>results...</p>
						{/if}
					{/if}
				{/if}
			{/if}
		{/if}
	{/if}
{:catch error}
	{error}
{/await}
