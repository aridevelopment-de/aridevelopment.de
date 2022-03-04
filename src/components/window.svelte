<script>
    export let id;
    export let title;
    export let x = 10;
    export let y = 50;
    export let width = 600;
    export let height = 400;
    export let minWidth = 300;
    export let minHeight = 200;
    // export let resizeX = true;
    // export let resizeY = true;
    export let onClose;

    const mouseBuffer = 5;

    let calculatedWidth = width;
    let calculatedHeight = height;

    let borderMouseState = 0;
    let headerMouseState = 0;
    let maximizeState = "inherit";
    
    const onMouseUp = () => {
        headerMouseState = 0;
        borderMouseState = 0;
    }
    const onBorderMouseDown = (event) => {
        if (event.clientX >= x && event.clientX <= x + calculatedWidth && Math.abs(event.clientY - y) <= mouseBuffer) {
            borderMouseState = 1;
        } else if (event.clientX >= x && event.clientX <= x + calculatedWidth && Math.abs(event.clientY - (y + calculatedHeight)) <= mouseBuffer) {
            borderMouseState = 2;
        } else if (event.clientY >= y && event.clientY <= y + calculatedHeight && Math.abs(event.clientX - x) <= mouseBuffer) {
            borderMouseState = 3;
        } else if (event.clientY >= y && event.clientY <= y + calculatedHeight && Math.abs(event.clientX - (x + calculatedWidth)) <= mouseBuffer) {
            borderMouseState = 4;
        }
    }
    const onMouseMove = (event) => {
        if (borderMouseState != 0) {
            if (borderMouseState == 1) {
                // top
                if (event.movementY > 0) {
                    if (calculatedHeight >= minHeight) {
                        calculatedHeight -= event.movementY;
                        y += event.movementY;
                    }
                } else {
                    calculatedHeight -= event.movementY;
                    y += event.movementY;
                }
            } else if (borderMouseState == 2) {
                // bottom
                if (event.movementY < 0) {
                    if (calculatedHeight >= minHeight) {
                        calculatedHeight += event.movementY;
                    }
                } else {
                    calculatedHeight += event.movementY;
                }
            } else if (borderMouseState == 3) {
                // left
                if (event.movementX > 0) {
                    if (calculatedWidth >= minWidth) {
                        calculatedWidth -= event.movementX;
                        x += event.movementX;
                    }
                } else {
                    calculatedWidth -= event.movementX;
                    x += event.movementX;
                }
            } else if (borderMouseState == 4) {
                // right
                if (event.movementX < 0) {
                    if (calculatedWidth >= minWidth) {
                        calculatedWidth += event.movementX;
                    }
                } else {
                    calculatedWidth += event.movementX;
                }
            }
        } else if (headerMouseState == 1) {
            if (document.body.style.cursor === "default") {
                x = Math.max(0, Math.min(x + event.movementX, window.innerWidth - calculatedWidth));
                y = Math.max(0, Math.min(y + event.movementY, window.innerHeight - calculatedHeight));
            }
        }
        
        if (borderMouseState == 0) {
            if (event.clientX >= x && event.clientX <= x + calculatedWidth && Math.abs(event.clientY - y) <= mouseBuffer) {
                if (document.body.hovering_windows.indexOf(id) == -1) {
                    document.body.hovering_windows.push(id);
                }

                document.body.style = "cursor: n-resize !important;";
            } else if (event.clientX >= x && event.clientX <= x + calculatedWidth && Math.abs(event.clientY - (y + calculatedHeight)) <= mouseBuffer) {
                if (document.body.hovering_windows.indexOf(id) == -1) {
                    document.body.hovering_windows.push(id);
                }

                document.body.style = "cursor: s-resize !important;";
            } else if (event.clientY >= y && event.clientY <= y + calculatedHeight && Math.abs(event.clientX - x) <= mouseBuffer) {
                if (document.body.hovering_windows.indexOf(id) == -1) {
                    document.body.hovering_windows.push(id);
                }

                document.body.style = "cursor: w-resize !important;";
            } else if (event.clientY >= y && event.clientY <= y + calculatedHeight && Math.abs(event.clientX - (x + calculatedWidth)) <= mouseBuffer) {
                if (document.body.hovering_windows.indexOf(id) == -1) {
                    document.body.hovering_windows.push(id);
                }

                document.body.style = "cursor: e-resize !important;";
            } else {
                // The problem: If the cursor is over anothers window border, this will still set the cursor to default
                if (document.body.hovering_windows.indexOf(id) != -1) {
                    document.body.hovering_windows.splice(document.body.hovering_windows.indexOf(id), 1)
                }

                if (document.body.hovering_windows.length == 0) {
                    document.body.style = "cursor: default";
                }
            }
        }
    };
    const switchMaximize = () => {
        if (maximizeState == 0) {
            maximizeState = 1;

            calculatedWidth = window.innerWidth;
            calculatedHeight = window.innerHeight;
            x = 0;
            y = 0;
        } else {
            maximizeState = 0;
            calculatedWidth = width;
            calculatedHeight = height;
        }
    }
</script>

<svelte:window on:mousemove={onMouseMove} on:mouseup={onMouseUp} />
<div class="container" on:mousedown={onBorderMouseDown} style="left: {x}px; top: {y}px; width: {calculatedWidth}px; height: {calculatedHeight}px;">
    <div class="header" on:mousedown={() => headerMouseState = 1}>
        <span class="title"> ID: {id} / {title} </span>
        <div class="header_icons">
            <svg height="20" stroke="white" width="20" class="minimize_icon">
                <line stroke-width="2" x1="2" x2="18" y1="15" y2="15"></line>
            </svg>
            <svg height="20" stroke="white" width="20" class="maximize_icon" on:click={switchMaximize}>
                {#if maximizeState == 1}
                    <rect fill="transparent" width="8" height="8" stroke-width="1" x="7" y="4"></rect>
                    <rect id="maximize_second_rect" width="8" height="8" stroke-width="1" x="4" y="7"></rect>
                {:else}
                    <rect fill="transparent" height="16" stroke-width="2" width="16" x="2" y="2"></rect>
                {/if}
            </svg>
            <svg height="20" stroke="white" width="20" class="close_icon" on:click={onClose}>
                <line stroke-width="2" x1="2" x2="18" y1="2" y2="18"></line>
                <line stroke-width="2" x1="18" x2="2" y1="2" y2="18"></line>
            </svg>
        </div>
    </div>
    <div class="contents" style="background-color: rgba(0, 0, 0, 0.7)">
        <slot />
    </div>
</div>

<style>
    :root {
        --header-height: 2rem;
        --header-color: #424242;
    }

    .container {
        position: absolute;
        box-shadow: 0 2px 2px rgba(0,0,0,.16),0 2px 2px rgba(0,0,0,.23);
        display: flex;
        flex-direction: column;
    }

    .header {
        background-color: var(--header-color);
        color: #fafafa;
        padding: 1px;
        display: flex;
        border-radius: 5px 5px 0 0;
        user-select: none;
        height: var(--header-height);
        align-items: center;
        border-bottom: 1px solid #262626;
    }

    .title {
        font-size: 18px;
        margin-left: 12px;
        flex: 1;
    }

    .header_icons {
        display: flex;
        margin-left: auto;
        margin-right: 0;
    }

    .header_icons * {
        margin-right: 8px;
        cursor: pointer;
    }

    .header_icons *:not(.close_icon):hover {
        filter: brightness(0.85);
    }

    .header_icons .close_icon:hover {
        stroke: rgb(255, 34, 34);
    }

    #maximize_second_rect {
        fill: var(--header-color);
    }

    .contents {
        flex-shrink: 0;
        flex-grow: 1;
        width: 100%;
        height: calc(100% - var(--header-height));
        overflow: hidden;
    }
</style>