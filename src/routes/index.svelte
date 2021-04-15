<script lang="ts">
	let files: FileList;
	let text: String;
	$: promise = files ? files[0].text().then((_text) => (text = _text)) : new Promise(() => {});
	$: columns = text ? text.split('\r\n')[0].split(',') : [];
	$: data = columns ? columns.reduce((acc, curr) => ((acc[curr] = ''), acc), {}) : [];

	let name: String;
	let calculations: String[] = [];
	let minimum;
	let maximum;
</script>

{@debug text}

<svelte:head>
	<title>framekiller</title>
</svelte:head>

<h1>framekiller</h1>
<h2>Find safe-jumps, meaties, and other frame-kills automatically given a set of frame-data.</h2>

<p>upload a frame-data csv:</p>
<small>or select a Google Drive document.</small>

<input type="file" bind:files />

{#await promise then resolved}
	{#if resolved}
		{#if columns}
			<p>select the name column:</p>
			{#each columns as column}
				<code>
					<label>
						<input type="radio" bind:group={name} value={column} />
						<span>{column}</span>
					</label>
				</code>
			{/each}
			{#if name}
				<p>select the calculation column(s):</p>
				{#each columns as column}
					{#if column != name}
						<code>
							<label>
								<input type="checkbox" bind:group={calculations} value={column} />
								<span>{column}</span>
							</label>
						</code>
					{/if}
				{/each}
				{#if calculations.length}
					<p>set the frame-search range:</p>
					<label>
						<input type="number" min="1" bind:value={minimum} />
						<span>minimum {minimum || 'x'} frames.</span>
					</label>
					<label>
						<input type="number" min="1" bind:value={maximum} />
						<span>maximum {maximum || 'x'} frames.</span>
					</label>
					{#if minimum && maximum}
						<p>results...</p>
					{/if}
				{/if}
			{/if}
		{/if}
	{/if}
{:catch error}
	{error}
{/await}
