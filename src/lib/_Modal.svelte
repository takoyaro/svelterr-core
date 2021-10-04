<script lang="ts">
    import {createEventDispatcher} from 'svelte';
   
    export let logs:ILog;

    const dispatch = createEventDispatcher();
   
    interface IUnhandledRejection{
        acc:'unhandledRejection',
        error:string
    }
    interface IError{
        acc:'error',
        error:string,
        url:string,
        line:number,
        col:number,
        stack:string,
        fileOnDisk:string,
        formatted?:boolean
    }
    type ILog = (IUnhandledRejection|IError)[];
</script>

<section>
    <h1>SveltErr</h1>
    {#if logs}
        {#if logs.length}
            {#each logs as log}
            <main>
                {#if log.acc=="error"}
                    <div class="entry">
                        <div class="row">
                            <span class="linecol">[{log.line}<span class="colon">:</span>{log.col}]</span>
                            <a class="path" href="vscode://file/{log.fileOnDisk}:{log.line}">{log.url}</a>
                        </div>
                        <pre class="errmsg">{log.error}</pre>
                    </div>
                {:else if log.acc=="unhandledRejection"}
                    <p>
                        UNHANDLED PROMISE REJECTION: {log.error}
                    </p>
                {/if}
            </main>
            {/each}
        {/if}
    {/if}
    <a on:click={()=>{dispatch('close')}}>Close</a>
</section>

<style>
    section{
        position:absolute !important;
        width:50% !important;
        height:50% !important;
        top:25% !important;
        left:25% !important;
        background-color:rgba(0,0,0,0.9) !important;
        box-shadow:1rem 1rem 2rem rgba(0,0,0,0.5) !important;
        border-radius:1rem !important;
        color:white !important;
        padding:1rem !important;
        font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif !important;
    }
    h1{
        font-size:1.2rem !important;
        color:red !important;
        font-weight:bold !important;
    }
    p,.entry{
        padding:8px !important;
        background-color:rgba(40,40,40,0.9) !important;
        width:100% !important;
        margin:4px 0px !important;
        border-radius:5px !important;
    }
    main{
        display:flex !important;
        flex-direction:column !important;
        width:100% !important;
    }
    .row{
        display:flex !important;
        flex-direction:row !important;
        margin-bottom:8px !important;
        gap:4px !important;
    }
    .linecol{
        color:hsl(10deg,80%,50%) !important;
    }
    .colon{
        color:rgb(253, 225, 113) !important;
    }
    .path{
        font-size:0.8rem  !important;
        line-height: 1.6rem  !important;
        color:hsl(50deg, 100%, 50%) !important;
    }
    .errmsg{
        background-color:rgba(60,60,60,0.9)  !important;
        padding:8px !important;
        border-radius: 5px !important;
    }
</style>