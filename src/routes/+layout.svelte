<script>
	import '../app.css';
	import BottomInfoBar from '../components/BottomInfoBar.svelte';
</script>

<div class="page-container">
	<main class="content-wrapper p-4">
		<slot />
	</main>
	<BottomInfoBar />
</div>

<style>
	body,
	html {
		height: 100%;
		margin: 0;
	}

	.page-container {
		display: flex;
		flex-direction: column;
		min-height: 100vh; /* Use the full viewport height */
	}

	.content-wrapper {
		flex: 1; /* This ensures that it takes all the available space pushing the footer down */
	}
</style>
