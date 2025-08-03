<script lang="ts">
	import Header from './Header.svelte';
	import '../app.css';
	
	let { children } = $props();
</script>

<div class="app">
	<Header />

	<main class="main-content">
		{@render children()}
	</main>
</div>

<style>
	.app {
		min-height: 100vh;
		width: 100%;
		overflow-x: hidden;
	}

	.main-content {
		width: 100%;
		min-height: 100vh;
		/* Account for fixed header */
		padding-top: 0;
		position: relative;
	}
</style>
