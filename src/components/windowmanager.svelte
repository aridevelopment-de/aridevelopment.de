<script>
    import TextEditor from './applications/texteditor.svelte';

    let open_windows = [
        // {type: "texteditor", title: "Text Editor", data: {content: "This is a text editor"}}
    ];

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
    }
    const onClose = (idx) => {
        open_windows.pop(idx);
        open_windows = open_windows;
    }
</script>

<svelte:window on:openapplication={openApplication} />
{#each open_windows as ow, idx}
	{#if ow.type === "texteditor"}
		<TextEditor id={idx} title={ow.title} data={ow.data} onClose={() => onClose(idx)}/>
	{/if}
{/each}