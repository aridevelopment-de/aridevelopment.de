<script>
    import { custom_event } from 'svelte/internal';

    let applications = [
        {name: "Editor", type: "texteditor", img: "/texteditor.png", id: "editor", start: {x: 0, y: 0}, data: {content: "Hello World!"}}
    ];

    let ref;
    let draggedIdx = -1;

    const onDragStart = (idx, event) => {
        draggedIdx = idx;
        applications[idx].start.x = event.clientX;
        applications[idx].start.y = event.clientY;
    }
    const dragOver = (event) => {
        event.preventDefault();
        return false;
    }
    const dragEnd = (event) => {
        if (draggedIdx != -1) {
            let target = document.getElementById(applications[draggedIdx].id);

            var diffX = event.clientX - applications[draggedIdx].start.x;
            var diffY = event.clientY - applications[draggedIdx].start.y;
            var rect = target.getBoundingClientRect();

            var offset = { 
                top: rect.top + window.scrollY, 
                left: rect.left + window.scrollX, 
            };
            
            var newleft = offset.left + diffX;
            var newtop = offset.top + diffY;
            
            target.style = `left: ${newleft}px; top: ${newtop}px;`;
            
            applications[draggedIdx].start.x = 0;
            applications[draggedIdx].start.y = 0;
        }
    }

    const openApplication = (idx) => {
        var app = applications[idx];

        const event = custom_event(
            "openapplication", 
            {
                title: app.name,
                type: app.type,
                icon: app.img,
                data: app.data
            },
            true
        );

        ref.dispatchEvent(event);
    }
</script>

<svelte:window on:drop={dragEnd} on:dragover={dragOver} />
<div class="application_manager" bind:this={ref}>
    {#each applications as application, idx}
        <div id={application.id} class="app_wrapper" style="left: {0}; top: {0}" on:dblclick={() => openApplication(idx)} on:dragstart={(event) => onDragStart(idx, event)} draggable="true">
            <img width="80" height="80" src={application.img} alt={application.name}>
            <p class="no_us"> {application.name} </p>
        </div>
    {/each}
</div>

<style>
    .app_wrapper {
        position: absolute;
        display: flex;
        flex-direction: column;
        align-items: center;
        width: fit-content;
        height: fit-content;
        padding: 10px;
        border-radius: 14px;
        gap: 3px;
        cursor: pointer;
    }

    .app_wrapper:hover {
        background-color: rgba(0, 0, 0, 0.3);
    }

    .app_wrapper img {
        pointer-events: none;
    }

    .app_wrapper p {
        font-size: 14px;
        padding: 0;
        margin: 0;
        color: white;
    }
</style>