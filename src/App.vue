<template>
	<div>
    <h3>{{ appName }}</h3>
		<form>
			<div>
				<input
					type="text"
					placeholder="GitHub Username"
					@blur="isGitHubUsernameAvailable"
					v-model="username"
				/>
				{{ available }}
			</div>
			<div>
				<input type="text" placeholder="Work Space Folder" v-model="workspace" />
			</div>

			<button>Save</button>
		</form>

		<div ref="output"></div>
	</div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const workspace = ref("");
const username = ref("");
let available = ref("");

async function isGitHubUsernameAvailable() {
	const { data } = await axios.get(
		`http://localhost:2001/check-username?username=${username.value}`
	);

	data ? (available = "✅ Available") : (available = "❌ Taken");
}

const socket = new WebSocket(`ws://localhost:2001/`);

socket.addEventListener("open", () => {
	console.log("Connected to server");
	socket.send("Hello from browser");
});

socket.addEventListener("message", (event) => {
	const data = JSON.parse(event.data);

	workspace.value = data.dir;
	if (data.type === "server-dir") {
		console.log(`Server __dirname:\n${data.dir}\n`);
	}
});

socket.addEventListener("close", () => {
	console.log("WebSocket connection closed.");
});

const appName = import.meta.env.VITE_APP_NAME
</script>
