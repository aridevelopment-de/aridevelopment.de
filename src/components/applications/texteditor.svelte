<script>
    import Window from '../window.svelte';

    export let id;
    export let isFocussed;
    export let title;
    export let data;
    export let onClose;
    export let onFocus;
</script>

<Window {id} {isFocussed} {title} width={400} height={500} {onClose} {onFocus}>
    <textarea id="textarea">{data.content}</textarea>
</Window>

<style>
    #textarea {
        width: 100%;
        height: 100%;
        border: none;
        outline: none;
        resize: none;
        color: white;
        background-color: transparent;
        cursor: inherit;
    }
</style>