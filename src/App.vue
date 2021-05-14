<template>
	<div id="app">
		<MiniJob
			v-if="showYourComponent"
			@close="showYourComponent = false"
		/>
	</div>
</template>

<script>
import MiniJob from "./components/miniJob/MiniJob";

export default {
	name: "App",
	components: {
		MiniJob,
	},
	data() {
		return {
			showYourComponent: true,
			data: null,
		};
	},
	mounted() {
		if (window.alt === undefined) {
			window.alt = {
				emit: () => {},
				on: () => {},
			};
		}

		window.alt.on("YOUR_COMPONENT_ON", (data) => {
			this.data = data;
			this.showYourComponent = true;
		});
		window.alt.on("YOUR_COMPONENT_OFF", () => {
			this.showYourComponent = false;
		});
	},
};
</script>

<style>
@import "styles.css";
</style>
