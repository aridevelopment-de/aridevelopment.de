<script>
    import { onMount } from 'svelte';
    
    const DELETE_BUFFER = -50;
    const REMOVEABLE_BUFFER = 10;
    const messages = {};

    const on_message = (event) => {
        messages[Object.keys(messages).length] = {percentage: 110, content: event.detail.content};
    }

    const update_progressbars = () => {
        for (const progressbar_inner of document.getElementsByClassName("progressbar_inner")) {
            let key = progressbar_inner.dataset.id;
            let data = messages[key];

            data.percentage -= 0.3;
            
            if (data.percentage <= DELETE_BUFFER) {
                delete messages[key];

                try {
                    document.getElementById("usermessage-" + key).remove();
                } catch { /* empty because I don't know why this throws an error but it still works \_o_/ */ }
            } else {
                messages[key] = data;
            }
        }
    }

    onMount(() => {
        setInterval(update_progressbars, 1);
    })
</script>

<svelte:window on:newmessage={on_message} />
<ul class="messages">
    {#each Object.keys(messages).reverse() as key}
        <li class="usermessage {messages[key].percentage <= REMOVEABLE_BUFFER ? 'removeable' : ''}" id="usermessage-{key}">
            <header>System Message</header>
            <p>{messages[key].content}</p>
            <div class="progressbar">
                <div class="progressbar_inner" data-id={key} style="width: calc({messages[key].percentage}% - 40px);" /> <!-- progress bar inner -->
            </div>
        </li>
    {/each}
</ul>

<style>
    .messages {
        list-style: none;
        position: absolute;
        right: 5px;
        width: 25%;
    }

    @keyframes messageInAnimation {
        from { transform: translateX(120%); }
        to { transform: translateX(0%); }
    }

    .usermessage {
        width: 100%;
        height: fit-content;
        background-color: #161616;
        border-radius: 10px;
        margin-bottom: 20px;
        animation: messageInAnimation 2s cubic-bezier(0.41, 0, 0.1, 1) 0s;
        transition: transform 2s cubic-bezier(0.41, 0, 0.1, 1);
    }

    .usermessage.removeable {
        transform: translateX(120%);
    }

    .usermessage header {
        font-size: 17px;
        font-weight: 500;
        color: white;
        padding: 20px 20px 0;
    }

    .usermessage p {
        font-weight: 350;
        color: #e7e7e7;
        padding: 0 20px 0;
    }

    .progressbar {
        padding: 0 20px 20px;
        width: 100%;
        height: 3px;
        background-color: transparent;
        border-bottom-left-radius: 10px;
        border-bottom-right-radius: 10px;
    }

    .progressbar_inner {
        border-radius: 10px;
        height: 100%;
        background-color: #00ffffb9;
    }

</style>