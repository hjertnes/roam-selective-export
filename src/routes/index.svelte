<svelte:head>
	<title>Roam Selective Export</title>
</svelte:head>

<script>
	let error;
	let files;
	let data;
	let state;

	function download(filename, text) {
		const element = document.createElement('a');
		element.setAttribute('href', 'data:application/json;charset=utf-8,' + encodeURIComponent(text));
		element.setAttribute('download', filename);
		element.style.display = 'none';
		document.body.appendChild(element);
		element.click();
		document.body.removeChild(element);
	}

	function onChange(e){
		error = undefined;
		data = undefined;
		state = undefined;

		const file = e.target.files[0]
		var reader = new FileReader();

		reader.addEventListener('load', function(e) {
				data = JSON.parse(e.target.result);
				state = data.map(({ title }) => ({
					checked: true,
					title,
				}))
				console.log(state)
		});

		reader.addEventListener('error', function() {
				error = 'Error : Failed to read file';
		});

		reader.readAsText(file);

	}

	function onDownload(){
		download("export.json", JSON.stringify(state.filter(x => x.checked).map(x => data.find(y => x.title === y.title))))
	}

</script>

<h1>Roam Selective Export</h1>

<p>This is a tool for creating a new roam JSON export file containing parts of your notes. To make it easy to import a large section of one roam database to another, without having to import 10 at a time</p>

<p> Upload a JSON file from Roam: <input id="fileUpload" type="file" on:change={onChange}>, select the notes you want to keep and Download the new JSON <button on:click={onDownload}>Download</button></p>

{#if state}
	<h2>Notes</h2>
	{#each state as item}
		<li><input type="checkbox" bind:checked={item.checked}/>{item.title}</li>
	{/each}
{/if}
