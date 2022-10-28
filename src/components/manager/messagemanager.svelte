<script>
	import { onMount } from "svelte";

	const DELETE_BUFFER = -50;
	const REMOVEABLE_BUFFER = 10;
	const messages = [];

	const on_message = (event) => {
		messages.push({ percentage: 110, content: event.detail.content });
	};

	const update_progressbars = () => {
        if (messages.length === 0) return;
		
        let data = messages[0];
		data.percentage -= 0.4;

		if (data.percentage <= DELETE_BUFFER) {
			messages.shift();
		} else {
			messages[0] = data;
		}
	};

	onMount(() => {
		setInterval(update_progressbars, 1);
	});
</script>

<svelte:window on:newmessage={on_message} />
<ul class="messages">
	{#if messages.length > 0}
		<li class="usermessage {messages[0].percentage <= REMOVEABLE_BUFFER ? 'removeable' : ''}">
			<header>System Message</header>
			<p>{messages[0].content}</p>
			<div class="progressbar">
				<div class="progressbar_inner" style="width: calc({messages[0].percentage}% - 40px);" />
				<!-- progress bar inner -->
			</div>
		</li>
	{/if}
</ul>

<style>
	.messages {
		list-style: none;
		position: absolute;
		right: 5px;
		width: 25%;
	}

	@keyframes messageInAnimation {
		from {
			transform: translateX(120%);
		}
		to {
			transform: translateX(0%);
		}
	}

	.usermessage {
		width: 100%;
		height: fit-content;
		background-color: #161616;
		border-radius: 10px;
		margin-bottom: 20px;
		animation: messageInAnimation 1.5s cubic-bezier(0.41, 0, 0.1, 1) 0s;
		transition: transform 1.24s cubic-bezier(0.41, 0, 0.1, 1);
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
