<script>
// @ts-nocheck

	import {supabase} from '$lib/supabaseClient'
	import {readable, get} from 'svelte/store'
	import Post from '$lib/Post.svelte'
  
	/**
	 * @param {{ target: HTMLFormElement | undefined; }} e
	 */
	async function addPost(e) {
	  const formData = new FormData(e.target)
	  const {data, error} = await supabase
		.from('channels')
		.insert({
		  namech: formData.get('namech')
		})
	}
  
	const channels = readable(null, (set) => {
	  supabase
		.from('channels')
		.select('*')
		// @ts-ignore
		.then(({error, data}) => set(data))
  
	  // add subscription to supabase logic
	  const subscription = 
        supabase
            .channel('channels')
            .on('postgres_changes',{ event: 'INSERT', schema: '*' }, payload => {
                // @ts-ignore
                set([...get(channels), payload.new])
            })
            .subscribe()
            return () => supabase.removeChannel(subscription)
    })
  
  </script>
<!-- component -->
<form on:submit|preventDefault={addPost}>
	<input type="checkbox" id="my-modal-3" class="modal-toggle" />
		<div class="modal">
			<div class="modal-box relative">
				<label for="my-modal-3" class="btn btn-sm btn-circle absolute right-2 top-2">✕</label>
				<h3 class="text-lg font-bold">Masukan nama Chanel</h3>
				<p class="pay-4">
					<input type="text" name="namech" placeholder="Type a New Channel here" class="input input-bordered input-primary w-full max-w-xs"/>
				</p>
				<button class="btn btn-outline btn-primary ml-96">SIMPAN</button>
			</div>
		</div>
</form>



<!-- component -->
<div class="flex h-screen antialiased text-gray-800">
    <div class="flex flex-row h-full w-full overflow-x-hidden">
      <div class="flex flex-col py-20 pl-30 pr-2 w-64 bg-indigo-100 flex-shrink-0">
        <div class="flex flex-row items-center justify-center h-12 w-full">
		
          <div
            class="flex items-center justify-center rounded-3xl text-indigo-700 bg-indigo-300 h-10 w-10"
          >
			<svg 
				class="w-6 h-6" 
				fill="none" 
				stroke="red" 
				viewBox="0 0 24 24" 
				xmlns="http://www.w3.org/2000/svg">
				<path stroke-linecap="round" 
				stroke-linejoin="round" 
				stroke-width="2" 
				d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z">
				</path>
			</svg>
          </div>

          <div class="ml-2 font-bold text-2xl">Chat in aja</div>
        </div>
		
        <div
        	class="flex flex-col items-center bg-indigo-300 border border-gray-200 mt-4 w-full py-6 px-4 rounded-lg"
        >
		<!--Button-->
		<label for="my-modal-3" class="btn btn-accent">Masukan Chanel Obrolan</label>
          <div class="flex flex-col space-y-1 mt-4 -mx-2">
        </div>
        	<div class="text-sm font-semibold mt-2">Selamat datang</div>
        	<div class="text-xs text-gray-500">Silahkan, Masukan Chanel Chat</div>
        <div class="flex flex-col mt-8">
            <div class="flex flex-row items-center justify-between text-xs">
				<span class="font-bold">Channel</span>
			</div>
		  	<div class="flex flex-col space-y-1 mt-4 -mx-2 h-48 overflow-y-auto">
				{#if $channels}
					{#each $channels as post}
						<button class="flex flex-row items-center hover:bg-accent-focus rounded-xl p-1">
							<div class="animate-bounce flex items-center justify-center h-8 w-8 bg-accent rounded-full">
								<div class="avatar online">
									<div class="w-10 rounded-full">
									  <img src="https://cdn-2.tstatic.net/trends/foto/bank/images2/foto-chaeyoung-twice.jpg" />
									</div>
								</div>
							</div>
							<a href="/channel/{post.id}">
								<div class="ml-2 text-sm font-semibold">
									{post.namech}
								</div>
							</a>
						</button>
					{/each}
				{:else}
					Loading..
				{/if}
			</div>            
        </div>
    </div>
	</div>
	<div class="flex flex-col flex-auto h-full p-6">
        <div
          class="flex flex-col flex-auto flex-shrink-0 rounded-2xl bg-gray-100 h-full p-4"
        >
          <div class="flex flex-col h-full overflow-x-auto mb-4">
            <div class="flex flex-col h-full">
              <div class="grid grid-cols-12 gap-y-2">
                <div class="col-start-3 col-end-10 p-1 rounded-lg">      
                      <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/ec/Logo_of_TWICE.svg/1200px-Logo_of_TWICE.svg.png"/>
                </div>
                <div class="col-start-1 col-end-8 p-3 rounded-lg">
                  <div class="flex flex-row items-center">
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
	</div>
</div>