<template>
	<div>
		<form>
			<div>
				<input type="text" placeholder="GitHub Username" />
			</div>
			<div>
				<input type="text" placeholder="Work Space Folder" />
			</div>

			<button>Save</button>
		</form>

		<div ref="output"></div>
	</div>
</template>

<script setup>
const socket = new WebSocket(`ws://localhost:2001/`);

socket.addEventListener("open", () => {
	console.log("Connected to server");
	socket.send("Hello from browser");
});

socket.addEventListener("message", (event) => {
	const data = JSON.parse(event.data);
	if (data.type === "server-dir") {
		console.log(`Server __dirname:\n${data.dir}\n`);
	}
});

socket.addEventListener("close", () => {
	console.log("WebSocket connection closed.");
});
</script>
