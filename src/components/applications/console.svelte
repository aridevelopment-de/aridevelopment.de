<script>
	import Window from "../window.svelte";
	import { files } from "../../stores/files";
	import { ICON_SIZE } from "../../utils/static";

	export let id;
	export let isFocussed;
	export let title;
	export let data;
	export let onClose;
	export let onFocus;

	const echo =
		"<span style='color: #16c60c'>root@localhost</span><span style='color: #cccccc'>:<span style='color: #3b78c0'>~</span>#</span> ";
	let availableFiles;
	let ref;

	files.subscribe((value) => {
		availableFiles = value;
	});

	const commands = {
		clear: (args) => {
			ref.innerHTML = "";
		},
		echo: (args) => {
			ref.innerHTML += args.map((a) => a.replace(/"/g, "")).join(" ");
		},
		whoami: (args) => {
			ref.innerHTML += "root <a style='color: #00000000' href='https://youtu.be/nnQ541XG2bw'>(|_1(|< |/|3</a>";
		},
		ls: (args) => {
			ref.innerHTML += availableFiles
				.map((f) => {
					if (f.type === "texteditor") {
						return f.name;
					} else {
						return `<span style='color: #14c60d'>${f.name}</span>`;
					}
				})
				.join(" ");
		},
		touch: (args) => {
			if (args.length === 0) {
				ref.innerHTML += "touch: missing file operand";
			} else {
				const name = args[0];
				const file = availableFiles.find((f) => f.name === name);
				if (file) {
					ref.innerHTML += `touch: cannot touch '${name}': File exists`;
				} else {
					// {name: "l1nk-tr33", type: "texteditor", img: "/texteditor.png", id: "url-tree", start: {x: 0 * ICON_SIZE, y: 0 * ICON_SIZE}, data: {content: "GitHub: https://github.com/aridevelopment-de\nDiscord: https://aridevelopment.de/dc\n"}},
					files.update((f) => {
						f.push({
							name,
							type: "texteditor",
							img: "/texteditor.png",
							id: `texteditor-${name}-${availableFiles.length + 2}`,
							start: {
								x: Math.round(Math.random() * 10) * ICON_SIZE,
								y: Math.round(Math.random() * 10) * ICON_SIZE,
							},
							data: { content: "" },
						});
						return f;
					});
				}
			}
		},
		cat: (args) => {
			if (args.length === 0) {
				ref.innerHTML += "cat: missing file operand";
			} else {
				const name = args[0];
				const file = availableFiles.find((f) => f.name === name);
				if (file) {
					ref.innerHTML += file.data.content + "\n";
				} else {
					ref.innerHTML += `cat: ${name}: No such file or directory`;
				}
			}
		},
		rm: (args) => {
			if (args.length === 0) {
				ref.innerHTML += "rm: missing file operand";
			} else {
				const name = args[0];
				const file = availableFiles.find((f) => f.name === name);
				if (file) {
					files.update((f) => {
						return f.filter((f) => f.name !== name);
					});
				} else {
					ref.innerHTML += `rm: cannot remove '${name}': No such file or directory`;
				}
			}
		}
	};

	const onCommand = (command) => {
		const child = document.createElement("div");
		child.innerHTML = echo + command;
		ref.appendChild(child);

		const args = command.split(" ");
		const cmd = args.shift();

		if (commands[cmd.toLowerCase()]) {
			commands[cmd.toLowerCase()](args);
		} else {
			const child = document.createElement("div");
			child.innerHTML = `bash: ${cmd}: command not found`;
			ref.appendChild(child);
		}
	};
</script>

<Window {id} {isFocussed} {title} width={500} height={400} {onClose} {onFocus}>
	<div class="container">
		<div class="output" bind:this={ref} />
		<input
			type="text"
			id="command-input"
			on:keydown={(e) => {
				if (e.key == "Enter") {
					onCommand(e.target.value);
					e.target.value = "";
				}
			}}
		/>
	</div>
</Window>

<style>
	.container {
		display: flex;
		flex-direction: column;
		height: 100%;
	}

	.output {
		flex: 2 2;
		overflow: auto;
		color: #cccccc;
	}

	#command-input {
		background-color: rgba(0, 0, 0, 0.7);
		border: 1px solid black;
		color: #cccccc;
	}

	#command-input:focus {
		outline: none;
	}
</style>
