<script>
// @ts-nocheck

    import {supabase} from '$lib/supabaseClient'
    import {readable, get} from 'svelte/store'
  
    /**
	 * @type {unknown}
	 */
     export let id
    /**
	 * @type {any}
	 */
     export let namech
    /**
	 * @type {any}
	 */
     export let content

     export let name
  
    // @ts-ignore
    async function addComment({target}) {
      const formData = new FormData(target)
  
      const {data, error} = await supabase
        .from('comments')
        .insert({
          content: formData.get('comment'),
          name: formData.get('names'),
          channel: id
        })
    }
  
    const comments = readable(null, (set) => {
	  supabase
		.from('comments')
		.select('*')
        .filter('channel','eq', id)
		// @ts-ignore
		.then(({error, data}) => set(data))
  
	  // add subscription to supabase logic
	  const subscription = 
        supabase
            .channel('comments:channel=eq.' + id)
            .on('postgres_changes',{ event: '*', schema: '*' }, (payload) => {
                if (payload.eventType === 'INSERT') {
                // @ts-ignore
                set([...get(comments), payload.new])
            }
            })
            .subscribe()
            return () => supabase.removeChannel(subscription)
    })
  </script>
  
  <div class="flex h-screen antialiased text-gray-800">
    <div class="flex flex-row h-full w-full overflow-x-hidden">
      <div class="flex flex-col py-8 pl-6 pr-2 w-64 bg-indigo-100 flex-shrink-0">
        <div class="flex flex-row items-center justify-center h-12 w-full">
          <div
            class="flex items-center justify-center rounded-2xl text-indigo-700 bg-indigo-300 h-10 w-10"
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
          class="flex flex-col items-center bg-indigo-300 border bg-accent mt-4 w-full py-6 px-4 rounded-lg"
        >
        <a href="/"> 
          <button>
            <svg 
              class="animate-bounce ml-10 w-10 h-10" 
              fill="none" 
              stroke="currentColor" 
              viewBox="0 0 24 24" 
              xmlns="http://www.w3.org/2000/svg">
              <path 
              stroke-linecap="round" 
              stroke-linejoin="round" 
              stroke-width="2" 
              d="M12 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2M3 12l6.414 6.414a2 2 0 001.414.586H19a2 2 0 002-2V7a2 2 0 00-2-2h-8.172a2 2 0 00-1.414.586L3 12z">
            </path>
            </svg>
          <a/>
          </button>
  
          <div class="text-sm font-semibold mt-2">Kembali, Pilihan Chanel</div>
          <div class="text-xs text-bg-warning-content">Selamat datang !</div>
        </div>
        <div class="flex flex-col space-y-1 mt-4 -mx-2 h-48 overflow-y-auto">
          <button class="flex flex-row items-center hover:bg-accent-focus rounded-xl p-1">
            <div class="flex items-center justify-center h-8 w-8 bg-accent rounded-full mr-3">
              <div class="avatar online">
                <div class="w-10 rounded-full">
                  <img src="https://cdn-2.tstatic.net/trends/foto/bank/images2/foto-chaeyoung-twice.jpg" />
                </div>
              </div>
            </div>
            {namech}
          </button>
        </div>
      </div>
      <div class="flex flex-col flex-auto h-full p-6">
        <div class="flex flex-col flex-auto flex-shrink-0 rounded-2xl bg-indigo-300 bg-accent h-full p-4">
          <div class="flex flex-col h-full overflow-x-auto mb-4">
            <div class="flex flex-col h-full">
              <div class="grid grid-cols-12 gap-y-2">
                {#if $comments}
                  {#each $comments as contents}
                    <div class="col-start-1 col-end-8 p-3 rounded-lg">
                      <div class="flex flex-row items-center">
                        <div
                          class="flex items-center justify-center h-10 w-10 rounded-full bg-secondary flex-shrink-0"
                        >
                        <div class="avatar online">
                          <div class="w-10 rounded-full">
                            <img src="https://i.pinimg.com/736x/e8/17/e4/e817e41b510d50013d66c63196a40d11.jpg" />
                          </div>
                        </div>
                        </div>
                        <div class="chat chat-start">
                          <div class="chat-header">
                            {contents.name}
                            <time class="text-xs opacity-50"
                              >{contents.created_at[11]}{contents.created_at[12]}:{contents
                                .created_at[14]}{contents.created_at[15]}&nbsp;&nbsp;&nbsp;{contents
                                .created_at[8]}{contents.created_at[9]}-{contents
                                .created_at[5]}{contents.created_at[6]}-{contents
                                .created_at[2]}{contents.created_at[3]}</time
                            >
                          </div>
                          <div class="chat-bubble">{contents.content}</div>
                        </div>
                      </div>
                    </div>
                  {:else}
                    No comments yet!
                  {/each}
                {:else}
                  Loading..
                {/if}
              </div>
            </div>
          </div>
          <form on:submit|preventDefault={addComment}>
            <div class="flex flex-row items-center h-16 rounded-xl bg-white w-full px-4">
              <div class="pr-7">
                <input
                  type="text"
                  name="names"
                  placeholder="Nama"
                  class="flex w-full border rounded-xl focus:outline-none focus:border-indigo-300 pl-4 h-10"
                />
              </div>
              <input
                type="text"
                name="comment"
                placeholder="Komen!"
                class="flex w-full border rounded-xl focus:outline-none focus:border-indigo-300 pl-4 h-10"
              />
              <div class="flex-grow ml-4">
                <div class="relative w-full" />
              </div>
              <div class="ml-4">
                <button
                  class="flex items-center justify-center bg-primary hover:bg-accent-content rounded-xl text-white px-4 py-1 flex-shrink-0"
                >
                  <span>Krim</span>
                  <span class="ml-2">
                    <svg
                      class="w-4 h-4 transform rotate-45 -mt-px"
                      fill="none"
                      stroke="currentColor"
                      viewBox="0 0 24 24"
                      xmlns="http://www.w3.org/2000/svg"
                    >
                      <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="2"
                        d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"
                      />
                    </svg>
                  </span>
                </button>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>