<script>
  import Window from '../window.svelte';

  export let id;
  export let isFocussed;
  export let title;
  export let data;
  export let onClose;
  export let onFocus;
</script>

<Window {id} {isFocussed} {title} width={500} height={400} {onClose} {onFocus}>
  <p>Hallo Welt</p>
</Window>

<style>
  
</style>