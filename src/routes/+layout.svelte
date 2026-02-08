<script lang="ts">
	import './layout.css';
	import '@fontsource-variable/material-symbols-outlined/full.css';
	import '@fontsource-variable/inter';
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<div
	class="relative flex h-full min-h-screen w-full max-w-107.5 flex-col overflow-x-hidden bg-background-light shadow-2xl"
>
	{@render children()}
</div>

<style>
	:global(body) {
		font-family: 'Inter Variable', cursive;
		scroll-behavior: smooth !important;
		-webkit-font-smoothing: antialiased;
	}

	:global(.material-symbols-outlined) {
		font-family: 'Material Symbols Outlined Variable', sans-serif;
		font-variation-settings:
			'FILL' 0,
			'wght' 400,
			'GRAD' 0,
			'opsz' 24;
	}

	:global(.progress-ring-circle) {
		transition: stroke-dashoffset 0.35s;
		transform: rotate(-90deg);
		transform-origin: 50% 50%;
	}

	:global(body) {
		min-height: max(884px, 100dvh);
	}
</style>
