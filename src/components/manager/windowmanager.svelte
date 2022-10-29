<script>
    import TextEditor from '../applications/texteditor.svelte';
    import Console from '../applications/console.svelte';
    import { custom_event } from 'svelte/internal';
	import Url from '../applications/url.svelte';

    const MAX_WINDOWS = 5;
    let open_windows = [];
    let focussed_window_id;
    let ref;

    const openApplication = (event) => {
        // title
        // type
        // icon
        // data

        if (open_windows.filter(elem => elem != null).length >= MAX_WINDOWS) {
            const event = custom_event(
                "newmessage", 
                {
                    content: "Too many applications are already opened!"
                },
                true
            );

            ref.dispatchEvent(event);
        } else {
            open_windows.push({
                type: event.detail.type,
                title: event.detail.title,
                data: event.detail.data
            });

            open_windows = open_windows;
            focussed_window_id = open_windows.length - 1;
        }  
    }
    const onClose = (idx) => {
        if (idx == open_windows.length - 1) {
            open_windows.pop(idx);
            open_windows = open_windows;
        } else {
            open_windows[idx] = null;
        }
        focussed_window_id = focussed_window_id.length - 1;
    }
</script>

<svelte:window on:openapplication={openApplication} />
<div class="window_manager" bind:this={ref}>
    {#each open_windows as ow, idx}
        {#if ow != null}
            {#if ow.type === "texteditor"}
                <TextEditor id={idx} isFocussed={focussed_window_id == idx} title={ow.title} data={ow.data} onClose={() => onClose(idx)} onFocus={() => focussed_window_id = idx} />
            {/if}
            {#if ow.type === "console"}
                <Console id={idx} isFocussed={focussed_window_id == idx} title={ow.title} data={ow.data} onClose={() => onClose(idx)} onFocus={() => focussed_window_id = idx} />
            {/if}
            {#if ow.type === "url"}
                <Url data={ow.data} />
            {/if}
        {/if}
    {/each}
</div>