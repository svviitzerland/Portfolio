<script>
	import { untrack } from 'svelte';

	let {
		title,
		close,
		minimize,
		onfocus,
		onnavigate,
		zIndex = 20,
		initialX = 120,
		initialY = 90,
		initialWidth = 900,
		initialHeight = 620,
		initialSearch = '',
		children
	} = $props();

	const favorites = [
		{ label: 'Projects', name: 'projects', icon: '▰' },
		{ label: 'Blog', name: 'blog', icon: '✎' },
		{ label: 'Books', name: 'book', icon: '▤' }
	];

	let x = $state(untrack(() => initialX));
	let y = $state(untrack(() => initialY));
	let width = $state(untrack(() => initialWidth));
	let height = $state(untrack(() => initialHeight));
	let search = $state(untrack(() => initialSearch));
	let dragStart = $state(null);
	let resizeStart = $state(null);
	let maximized = $state(false);
	let previousFrame = null;
	let lastTitle = untrack(() => title);

	$effect(() => {
		if (title !== lastTitle) {
			search = '';
			lastTitle = title;
		}
	});

	function startDrag(event) {
		if (event.button !== 0 || event.target.closest('button, input')) return;
		onfocus?.();
		dragStart = { pointerX: event.clientX, pointerY: event.clientY, x, y };
		event.currentTarget.setPointerCapture(event.pointerId);
		event.preventDefault();
	}

	function moveDrag(event) {
		if (!dragStart) return;
		const maxX = Math.max(0, window.innerWidth - 120);
		const maxY = Math.max(36, window.innerHeight - 90);
		x = Math.min(maxX, Math.max(0, dragStart.x + event.clientX - dragStart.pointerX));
		y = Math.min(maxY, Math.max(36, dragStart.y + event.clientY - dragStart.pointerY));
	}

	function endDrag() {
		dragStart = null;
	}

	function startResize(event) {
		if (event.button !== 0) return;
		onfocus?.();
		resizeStart = { pointerX: event.clientX, pointerY: event.clientY, width, height };
		event.currentTarget.setPointerCapture(event.pointerId);
		event.preventDefault();
		event.stopPropagation();
	}

	function moveResize(event) {
		if (!resizeStart) return;
		width = Math.max(420, Math.min(window.innerWidth - x, resizeStart.width + event.clientX - resizeStart.pointerX));
		height = Math.max(320, Math.min(window.innerHeight - y - 12, resizeStart.height + event.clientY - resizeStart.pointerY));
	}

	function endResize() {
		resizeStart = null;
	}

	function toggleMaximize() {
		onfocus?.();
		if (maximized && previousFrame) {
			({ x, y, width, height } = previousFrame);
			maximized = false;
			return;
		}
		previousFrame = { x, y, width, height };
		x = 12;
		y = 38;
		width = window.innerWidth - 24;
		height = window.innerHeight - 106;
		maximized = true;
	}
</script>

<div
	class="finder-window fixed flex flex-col overflow-hidden rounded-xl border border-white/15 bg-[#202124]/95 text-white shadow-[0_24px_80px_rgba(0,0,0,0.48)] backdrop-blur-2xl"
	style="left: {x}px; top: {y}px; width: {width}px; height: {height}px; z-index: {zIndex};"
	role="dialog"
	aria-label={title}
	aria-modal="false"
	tabindex="-1"
	onpointerdown={onfocus}
>
	<!-- Finder toolbar and titlebar are also the drag handle. -->
	<header
		class="finder-toolbar flex h-[54px] shrink-0 items-center gap-4 border-b border-white/10 px-4"
		role="toolbar"
		aria-label="{title} window toolbar"
		tabindex="-1"
		onpointerdown={startDrag}
		onpointermove={moveDrag}
		onpointerup={endDrag}
		onpointercancel={endDrag}
	>
		<div class="flex shrink-0 items-center gap-2">
			<button class="traffic-light bg-[#ff5f57]" aria-label="Close window" onclick={(event) => { event.stopPropagation(); close(); }} onpointerdown={(event) => event.stopPropagation()}></button>
			<button class="traffic-light bg-[#febc2e]" aria-label="Minimize window" onclick={(event) => { event.stopPropagation(); minimize(); }} onpointerdown={(event) => event.stopPropagation()}></button>
			<button class="traffic-light bg-[#28c840]" aria-label="Zoom window" onclick={(event) => { event.stopPropagation(); toggleMaximize(); }} onpointerdown={(event) => event.stopPropagation()}></button>
		</div>

		<div class="flex shrink-0 items-center gap-1 rounded-lg bg-white/[0.06] p-1">
			<button class="toolbar-icon" aria-label="Back" title="Back" disabled>‹</button>
			<button class="toolbar-icon" aria-label="Forward" title="Forward" disabled>›</button>
		</div>

		<h2 class="min-w-0 flex-1 truncate text-center text-[13px] font-semibold text-white/90">{title}</h2>
		<button class="mobile-app-close" aria-label="Close {title}" onclick={(event) => { event.stopPropagation(); close(); }}>‹</button>

		<div class="flex shrink-0 items-center gap-2">
			<div class="relative hidden w-40 sm:block">
				<svg class="absolute left-2 top-1/2 h-3.5 w-3.5 -translate-y-1/2 text-white/45" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" aria-hidden="true">
					<path stroke-linecap="round" stroke-linejoin="round" d="m21 21-5.197-5.197m0 0A7.5 7.5 0 1 0 5.196 5.196a7.5 7.5 0 0 0 10.607 10.607Z" />
				</svg>
				<input type="search" placeholder="Search" bind:value={search} class="h-7 w-full rounded-md border border-white/10 bg-black/20 pl-7 pr-2 text-xs text-white/90 outline-none placeholder:text-white/40 focus:border-white/25" />
			</div>
		</div>
	</header>

	<div class="flex min-h-0 flex-1">
		<aside class="finder-sidebar hidden w-44 shrink-0 overflow-y-auto border-r border-white/[0.08] px-2 py-4 sm:block">
			<p class="px-2 pb-2 text-[10px] font-semibold uppercase tracking-[0.12em] text-white/40">Favorites</p>
			{#each favorites as favorite (favorite.label)}
				<button
					class="flex h-8 w-full items-center gap-2 rounded-md px-2 text-left text-[12px] text-white/75 transition-colors hover:bg-white/10 hover:text-white"
					onclick={() => onnavigate(favorite.name)}
				>
					<span class="w-4 text-center text-[15px] text-sky-400">{favorite.icon}</span>
					<span>{favorite.label}</span>
				</button>
			{/each}
			<div class="my-3 border-t border-white/[0.08]"></div>
			<p class="px-2 pb-2 text-[10px] font-semibold uppercase tracking-[0.12em] text-white/40">Locations</p>
			<div class="flex h-8 items-center gap-2 px-2 text-[12px] text-white/60"><span class="w-4 text-center text-white/40">☁</span>iCloud Drive</div>
			<div class="flex h-8 items-center gap-2 px-2 text-[12px] text-white/60"><span class="w-4 text-center text-white/40">⌂</span>Farhan</div>
		</aside>

		<main class="finder-content min-w-0 flex-1 select-text overflow-auto">
			{@render children(search)}
		</main>
	</div>

	<button
		type="button"
		class="finder-resize absolute bottom-1 right-1 h-5 w-5 cursor-nwse-resize appearance-none border-0 bg-transparent p-0"
		aria-label="Resize window"
		onpointerdown={startResize}
		onpointermove={moveResize}
		onpointerup={endResize}
		onpointercancel={endResize}
	></button>
</div>

<style>
	.finder-window {
		-webkit-user-select: none;
		user-select: none;
	}

	.finder-toolbar {
		touch-action: none;
		cursor: grab;
	}

	.finder-toolbar:active {
		cursor: grabbing;
	}

	.traffic-light {
		position: relative;
		height: 12px;
		width: 12px;
		border-radius: 9999px;
		box-shadow: inset 0 0 0 1px rgb(0 0 0 / 12%);
	}

	.traffic-light::after {
		position: absolute;
		inset: 0;
		content: '';
		border-radius: inherit;
		background: linear-gradient(180deg, rgb(255 255 255 / 24%), transparent 62%);
	}

	.toolbar-icon {
		display: grid;
		height: 24px;
		width: 24px;
		place-items: center;
		border-radius: 5px;
		color: rgb(255 255 255 / 72%);
		font-size: 23px;
		line-height: 1;
	}

	.toolbar-icon:disabled {
		color: rgb(255 255 255 / 30%);
	}

	.finder-content :global(.window-scroll) {
		max-height: 100%;
	}

	.finder-resize::after {
		position: absolute;
		inset: 6px;
		content: '';
		border-right: 2px solid rgb(255 255 255 / 28%);
		border-bottom: 2px solid rgb(255 255 255 / 28%);
	}

	.finder-resize {
		touch-action: none;
	}

	@media (max-width: 639px) {
		.finder-window {
			inset: 0 !important;
			width: 100vw !important;
			height: 100dvh !important;
			border: 0;
			border-radius: 0;
			padding-top: env(safe-area-inset-top);
			padding-bottom: env(safe-area-inset-bottom);
			background: #111318;
			backdrop-filter: none;
		}

		.finder-toolbar {
			height: 52px;
			padding-inline: 18px;
			border-bottom-color: rgb(255 255 255 / 10%);
			cursor: default;
		}

		.finder-toolbar > div:first-child,
		.finder-toolbar > div:nth-child(2),
		.finder-toolbar > div:last-child {
			display: none;
		}

		.finder-toolbar h2 {
			text-align: left;
			font-size: 17px;
		}

		.finder-toolbar .mobile-app-close {
			display: grid;
			width: 36px;
			height: 36px;
			place-items: center;
			border-radius: 50%;
			background: rgb(255 255 255 / 9%);
			color: white;
			font-size: 30px;
			line-height: 1;
		}

		.finder-content {
			padding-bottom: 16px;
		}
	}

	.mobile-app-close { display: none; }

	@media (prefers-reduced-motion: no-preference) {
		.finder-window {
			animation: finder-open 180ms cubic-bezier(0.2, 0.8, 0.2, 1) both;
		}
	}

	@keyframes finder-open {
		from { opacity: 0; transform: scale(0.985) translateY(8px); }
		to { opacity: 1; transform: scale(1) translateY(0); }
	}
</style>
