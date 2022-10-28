<script>
	import { custom_event, onMount } from "svelte/internal";
	import { files } from "../../stores/files";
	import { ICON_SIZE } from "../../utils/static";

	let applications;

	files.subscribe((value) => {
        // get all new entries
        let newEntries = value.filter((file) => !applications.find((app) => app.name === file.name));
        applications = value;

        setTimeout(() => {newEntries.forEach(() => {
            for (let app of newEntries) {
                let target = document.getElementById(app.id);
                target.style.left = app.start.x + "px";
                target.style.top = app.start.y + "px";
            }
        })}, 1);
	});

	onMount(() => {
        let apps = [
			{
				name: "l1nk-tr33",
				type: "texteditor",
				img: "/texteditor.png",
				id: "url-tree",
				start: { x: 0 * ICON_SIZE, y: 0 * ICON_SIZE },
				data: {
					content: "GitHub: https://github.com/aridevelopment-de\nDiscord: https://aridevelopment.de/dc\n",
				},
			},
			{
				name: "cmd",
				type: "console",
				img: "/cmd.png",
				id: "console-entry",
				start: { x: 1 * ICON_SIZE, y: 0 * ICON_SIZE },
				data: null,
			},
		];
		files.set(apps);

        setTimeout(() => {apps.forEach(() => {
            for (let app of apps) {
                let target = document.getElementById(app.id);
                target.style.left = app.start.x + "px";
                target.style.top = app.start.y + "px";
            }
        })}, 1);
	});

	let ref;
	let draggedIdx = -1;

	const onDragStart = (idx, event) => {
		draggedIdx = idx;
		applications[idx].start.x = event.clientX;
		applications[idx].start.y = event.clientY;
	};
	const dragOver = (event) => {
		event.preventDefault();
		return false;
	};
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

			var newLeft = offset.left + diffX;
			var newTop = offset.top + diffY;

			newLeft = Math.round(newLeft / ICON_SIZE);
			newTop = Math.round(newTop / ICON_SIZE);
			newLeft = Math.max(0, newLeft);
			newTop = Math.max(0, newTop);
			newLeft *= ICON_SIZE;
			newTop *= ICON_SIZE;

			target.style = `left: ${newLeft}px; top: ${newTop}px;`;

			applications[draggedIdx].start.x = 0;
			applications[draggedIdx].start.y = 0;
		}
	};

	const openApplication = (idx) => {
		var app = applications[idx];

		const event = custom_event(
			"openapplication",
			{
				title: app.name,
				type: app.type,
				icon: app.img,
				data: app.data,
			},
			true
		);

		ref.dispatchEvent(event);
	};
</script>

<svelte:window on:drop={dragEnd} on:dragover={dragOver} />
<div class="application_manager" bind:this={ref}>
	{#each applications as application, idx}
		<div
			id={application.id}
			class="app_wrapper"
			style="left: {0}; top: {0}"
			on:dblclick={() => openApplication(idx)}
			on:dragstart={(event) => onDragStart(idx, event)}
			draggable="true"
		>
			<img width="80" height="80" src={application.img} alt={application.name} />
			<p class="no_us">{application.name}</p>
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
