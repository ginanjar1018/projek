<script>
// @ts-nocheck

	import { onMount } from 'svelte';
	import { supabase } from '$lib/supabaseClient';


	/**
	 * @type {any[] | null}
	 */
	let channels;

	// $: filteredChannel = channels?.filter((element) => element.title.includes(searchText));

	/**
	 * @type {string}
	 */
	let title;
	/**
	 * @type {string}
	 */
	let namech;

	/**
	 * @type {string}
	 */
	let detail;

	/**
	 * @type {string}
	 */
	let searchText;

	async function getChannel() {
		//select * from channel
		let { data, error: err } = await supabase
			.from('channel')
			.select('*')
			.order('created_at', { ascending: false });
		channels = data;
	}

	async function postNewChannel() {
		//insert into channel (title, posted_by, detail, votes)
		//values ("halo", "levi", "test halo", 0)
		let { data, error } = await supabase.from('channel').insert({
			namech: namech
		});
		getChannel();
		namech = ''
		
	}

	onMount(function () {
		getChannel();
	});
</script>

<div class="container mx-auto p-8 ml-60 max-w-full">
	<!-- The button to open modal -->
	<label for="my-modal-6" class="btn">Post your discussion</label>

	<!-- Put this part before </body> tag -->
	<input type="checkbox" id="my-modal-6" class="modal-toggle" />
	<div class="modal modal-bottom sm:modal-middle">
		<div class="modal-box">
			<h1 class="text-xl">Post to Forum</h1>
			<input
				type="text"
				placeholder="Name Channel"
				class="input input-bordered input-info w-full max-w-xs mt-5"
				bind:value={namech}
			/><br />
			<div class="modal-action">
				<button class="p-2 px-16 mt-5 bg-blue-500 rounded text-white" on:click={postNewChannel}
					>Submit</button
				>
				<label for="my-modal-6" class="btn mt-5">Close!</label>
			</div>
		</div>
	</div>

	<div class="w-3/5 mt-10">
		<h1 class="text-3xl	text-bold mb-5">Channel</h1>
		<!-- <input bind:value={searchText} class="p-2 m-2 border rounded" placeholder="Title to search" /> -->
		{#if channels}
			{#each channels as channel}
				<div class="card w-116 bg-blue-500 text-primary-content mb-10">
					<div class="card-body">
						<a href="/channel/{channel.id}">
							<h2 class="card-title">{channel.namech}</h2>
						</a>
					</div>
				</div>
			{/each}
		{/if}
	</div>
</div>
