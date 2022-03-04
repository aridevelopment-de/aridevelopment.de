<script>
    import ApplicationManager from './applicationmanager.svelte';
</script>

<div class="desktop">
    <img src="tsumiki.png" alt="tsumiki" id="tsumiki" class="no_us" draggable="false">
    <ApplicationManager />
    <slot />
</div>

<style>
    #tsumiki {
        pointer-events: none;
        width: 40%;
        aspect-ratio: 1;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        z-index: -3;
        filter:
            saturate(1.2)
            brightness(0.9) 
            drop-shadow(0 0 30px rgb(170, 111, 204)) 
            blur(0.75px);
        opacity: 0.5;
    }
</style>