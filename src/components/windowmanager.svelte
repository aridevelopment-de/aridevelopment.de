<script>
    import TextEditor from './applications/texteditor.svelte';

    let open_windows = [];
    let focussed_window_id;

    const openApplication = (event) => {
        // title
        // type
        // icon
        // data

        open_windows.push({
            type: event.detail.type,
            title: event.detail.title,
            data: event.detail.data
        });

        open_windows = open_windows;
        focussed_window_id = open_windows.length - 1;        
    }
    const onClose = (idx) => {
        open_windows.pop(idx);
        open_windows = open_windows;
        focussed_window_id = focussed_window_id.length - 1;
    }
</script>

<svelte:window on:openapplication={openApplication} />
<div class="window_manager">
    {#each open_windows as ow, idx}
        {#if ow.type === "texteditor"}
            <TextEditor id={idx} isFocussed={focussed_window_id == idx} title={ow.title} data={ow.data} onClose={() => onClose(idx)} onFocus={() => focussed_window_id = idx} />
        {/if}
    {/each}
</div>