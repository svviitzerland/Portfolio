<script>
	import '@fontsource/inter/400.css';
	import '@fontsource/inter/600.css';
	import '@fontsource/outfit/700.css';
	import '@fontsource/outfit/900.css';
	import './layout.css';
	import { page } from '$app/stores';
	import { spring } from 'svelte/motion';
	import { resolve } from '$app/paths';

	let { children } = $props();

	// Custom Cursor Logic
	let coords = spring({ x: 0, y: 0 }, { stiffness: 0.05, damping: 0.25 });
	let size = spring(10, { stiffness: 0.1, damping: 0.25 });

	function handleMouseMove(e) {
		coords.set({ x: e.clientX, y: e.clientY });
	}

	function handleMouseDown() {
		size.set(20);
	}

	function handleMouseUp() {
		size.set(10);
	}
</script>

<svelte:window
	on:mousemove={handleMouseMove}
	on:mousedown={handleMouseDown}
	on:mouseup={handleMouseUp}
/>

<!-- Custom Cursor -->
<div
	class="hidden md:block fixed top-0 left-0 pointer-events-none z-[100] mix-blend-difference"
	style="transform: translate({$coords.x}px, {$coords.y}px)"
>
	<div
		style="width: {$size}px; height: {$size}px;"
		class="bg-white rounded-full -translate-x-1/2 -translate-y-1/2"
	></div>
</div>

<div
	class="font-sans antialiased text-zinc-50 selection:bg-zinc-300 selection:text-zinc-900 bg-zinc-950 min-h-screen flex flex-col items-center overflow-x-hidden selection:bg-white/20"
>
	{#if $page.url.pathname.startsWith('/blog/')}
	<nav
		class="liquid-glass-surface fixed top-2 left-2 right-2 z-50 flex h-7 items-center justify-between rounded-xl px-4 select-none"
	>
		<a
			href={resolve('/')}
			class="text-[12px] font-medium text-slate-900/75 hover:text-slate-950 transition-colors flex items-center gap-1.5"
		>
			<svg class="w-3 h-3" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
				<path stroke-linecap="round" stroke-linejoin="round" d="M15.75 19.5 8.25 12l7.5-7.5" />
			</svg>
			Desktop
		</a>
		<span class="text-[12px] text-slate-900/70">Farhan Aulianda</span>
		<a
			href={resolve('/?open=blog')}
			class="text-[12px] font-medium text-slate-900/75 hover:text-slate-950 transition-colors"
		>
			All Posts
		</a>
	</nav>
	{/if}

	{@render children()}
</div>

<style>
	:global(html) {
		scroll-behavior: smooth;
		background-color: #09090b; /* zinc-950 */
	}
	:global(body) {
		font-family: 'Inter', sans-serif;
	}
	:global(h1, h2, h3, h4, h5, h6) {
		font-family: 'Outfit', sans-serif;
	}
</style>
