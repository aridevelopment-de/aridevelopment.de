<script>
	import Desktop from './components/desktop.svelte';
	import WindowManager from './components/manager/windowmanager.svelte';

	document.body.hovering_windows = [];
</script>

<Desktop>
	<WindowManager />
</Desktop>

<style>
	
</style>