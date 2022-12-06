<script>
// @ts-nocheck

	import { supabase } from '$lib/supabaseClient';
	import { page } from '$app/stores';
    import { readable, get } from 'svelte/store'
    import Post from '$lib/Post.svelte';

    let id = $page.params.id;
    export let namech

    const channels = readable(null, (set) =>{
        
        supabase
            .from('channels')
            .select('*')
            .filter('id','eq',id)
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
            

    })

</script>

{#if $channels }
        {#each $channels as {namech, id} }
            <Post {namech} {id} />
        {/each}
{:else}
 Loading..
{/if}