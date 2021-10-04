<script lang="ts">
    import { browser } from "$app/env";
    import Modal from './_Modal.svelte';
    import {createEventDispatcher} from 'svelte';

    const dispatcher = createEventDispatcher();
    let logs = [];
    let _modal;

    if(browser){
        window.onerror = async function(error, url, line, col, err) {
            console.log(url);
            let formattedStack = await(getSourceMapStacktrace(url,err.stack));
            if(formattedStack){
                logs.push({
                    acc:'error',
                    error:formattedStack.err,
                    url:url,
                    line:formattedStack.line,
                    col:formattedStack.col,
                    fileOnDisk:formattedStack.fileOnDisk,
                    formatted:true
                });
            }
            logs = logs;
            if(!_modal){
                _modal = new Modal({
                    target:document.body,
                    props: {
                        logs:logs
                    }
                });
                (_modal as Modal).$on('close',(e)=>{
                    logs = [];
                    (_modal as Modal).$destroy();
                    _modal = null;
                })
            }
            else{
                (_modal as Modal).$set({logs:logs})
            }
            dispatcher('error',{
                    acc:'error',
                    error:formattedStack.err,
                    url:url,
                    line:formattedStack.line,
                    col:formattedStack.col,
                    fileOnDisk:formattedStack.fileOnDisk,
                    formatted:true
            })
        };
        window.onunhandledrejection = async (e)=>{
            console.log(e.reason.stack);
            if(e.reason.stack.includes('Failed to fetch')){
                logs.push({acc:'unhandledRejection', error:e.reason});
            }
            else{
                let stack = e.reason.stack;
                let rawUrl = stack.match(/\(http(.*?)\)/gm)[0].replace(/\(|\)/g,'');
                let urlChunks = rawUrl.split(':');
                let url = urlChunks.slice(0,urlChunks.length-2).join(':')
                let formattedStack = await(getSourceMapStacktrace(url,e.reason.stack));
                if(formattedStack){
                    logs.push({
                        acc:'error',
                        error:formattedStack.err,
                        url:url,
                        line:formattedStack.line,
                        col:formattedStack.col,
                        fileOnDisk:formattedStack.fileOnDisk,
                        formatted:true
                    });
                }
            }
            
            if(!_modal){
                _modal = new Modal({
                    target:document.body,
                    props: {
                        logs:logs
                    }
                });
                (_modal as Modal).$on('close',(e)=>{
                    logs = [];
                    (_modal as Modal).$destroy();
                    _modal = null;
                })
            }
            else{
                (_modal as Modal).$set({logs:logs})
            }
            dispatcher('unhandledRejection',{acc:'unhandledRejection', error:e.reason});
            
        }
    }
    async function getSourceMapStacktrace(url,stackTrace){
        const req = await fetch(`SveltErr/${encodeURIComponent(url)}.json?stack=${encodeURIComponent(stackTrace)}`);
        const res = await req.json();
        return res as null|{err:string,file:string,line:number,col:number,fileOnDisk:string}
    }
</script>