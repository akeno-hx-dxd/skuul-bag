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
	/* Chrome scrollbar fix at layout level */
	:global(html::-webkit-scrollbar),
	:global(body::-webkit-scrollbar) {
		display: none !important;
		width: 0px !important;
		height: 0px !important;
		background: transparent !important;
	}

	:global(html),
	:global(body) {
		scrollbar-width: none !important;
		-ms-overflow-style: none !important;
		overflow-y: auto !important;
		overflow-x: hidden !important;
	}

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
