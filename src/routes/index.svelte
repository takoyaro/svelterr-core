<script lang="ts">
    import SveltErr from '$lib/SveltErr.svelte';

    async function makeErr(){
        let test = {};
        test['boy'].replace('wow','');
    }
</script>

<button on:click={()=>{a=b+c;}}>Generate an error</button>
<button on:click={makeErr}>Generate another kind of error</button>
<button on:click={()=>{fetch('https://google.com')}}>Generate an Unhandled Promise Rejection</button>

<SveltErr/>
<style>
    button{
        background-color:hsl(100deg,0%,80%);
        padding:8px;
        border-radius:3px;
    }
</style>