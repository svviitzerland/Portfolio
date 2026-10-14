<script>
	import MenuBar from '$lib/components/desktop/MenuBar.svelte';
	import Icon from '$lib/components/Icon.svelte';
	import WeatherWidget from '$lib/components/WeatherWidget.svelte';
	import DesktopWindow from '$lib/components/desktop/DesktopWindow.svelte';
	import Dock from '$lib/components/Dock.svelte';
	import ProjectsContent from '$lib/components/desktop/ProjectsContent.svelte';
	import BlogContent from '$lib/components/desktop/BlogContent.svelte';
	import BooksContent from '$lib/components/desktop/BooksContent.svelte';
	import SpotifyContent from '$lib/components/desktop/SpotifyContent.svelte';
	import ResumeContent from '$lib/components/ResumeContent.svelte';
	import { page } from '$app/stores';
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';

	let { data } = $props();

	let windows = $state([]);
	let brightness = $state(100);
	let nextWindowId = 1;
	let highestZIndex = 20;
	let windowLimitNotice = $state(false);
	let windowLimitTimer;
	const MAX_DESKTOP_WINDOWS = 8;

	const posts = $derived(data?.posts || []);
	const windowTitles = {
		projects: 'Projects',
		blog: 'Blog',
		book: 'Books',
		dossier: 'Farhan — Resume',
		spotify: 'Spotify'
	};

	// Desktop icon positions with localStorage persistence
	const POSITIONS_KEY = 'desktop-icon-positions';
	const defaultPositions = {
		dossier: { x: 20, y: 60 },
		projects: { x: 20, y: 160 },
		blog: { x: 20, y: 260 },
		book: { x: 20, y: 360 },
		spotify: { x: 20, y: 460 },
		nimbus: { x: 20, y: 560 }
	};

	let iconPositions = $state(structuredClone(defaultPositions));

	function savePositions() {
		try {
			localStorage.setItem(POSITIONS_KEY, JSON.stringify(iconPositions));
		} catch (e) {
			// localStorage may be unavailable (private browsing, etc.)
		}
	}

	function moveIcon(name, x, y) {
		iconPositions[name] = { x, y };
		savePositions();
	}

	// Auto-open folder from ?open= query param + load saved icon positions
	onMount(() => {
		const openParam = $page.url.searchParams.get('open');
		const requestedWindow = $page.url.searchParams.has('book') ? 'book' : openParam;
		if (requestedWindow && ['projects', 'blog', 'book', 'dossier', 'spotify'].includes(requestedWindow)) {
			openWindow(requestedWindow);
			// Clean URL without reloading
			goto('/', { replaceState: true, noScroll: true });
		}

		// Load saved icon positions from localStorage
		try {
			const saved = localStorage.getItem(POSITIONS_KEY);
			if (saved) {
				const parsed = JSON.parse(saved);
				// Merge with defaults to handle missing keys
				iconPositions = { ...defaultPositions, ...parsed };
			}
		} catch (e) {
			// Ignore parse errors, use defaults
		}
	});

	function openWindow(name) {
		if (!windowTitles[name]) return;
		if (windows.length >= MAX_DESKTOP_WINDOWS) {
			windowLimitNotice = true;
			clearTimeout(windowLimitTimer);
			windowLimitTimer = setTimeout(() => (windowLimitNotice = false), 1800);
			return;
		}

		const id = nextWindowId++;
		const width = Math.min(960, Math.max(420, window.innerWidth - 48));
		const height = Math.min(680, Math.max(320, window.innerHeight - 144));
		const cascade = windows.length % 7 * 30;
		const centeredX = Math.round((window.innerWidth - width) / 2);
		const centeredY = Math.round((window.innerHeight - height) / 2) - 18;

		windows = [
			...windows,
			{
				id,
				name,
				title: windowTitles[name],
				x: Math.max(8, Math.min(centeredX + cascade, window.innerWidth - 120)),
				y: Math.max(40, Math.min(centeredY + cascade, window.innerHeight - 110)),
				width,
				height,
				zIndex: ++highestZIndex,
				minimized: false
			}
		];
	}

	function focusWindow(id) {
		highestZIndex += 1;
		windows = windows.map((desktopWindow) =>
			desktopWindow.id === id ? { ...desktopWindow, zIndex: highestZIndex } : desktopWindow
		);
	}

	function navigateWindow(id, name) {
		if (!windowTitles[name]) return;
		windows = windows.map((desktopWindow) =>
			desktopWindow.id === id
				? { ...desktopWindow, name, title: windowTitles[name] }
				: desktopWindow
		);
		focusWindow(id);
	}

	function closeWindow(id) {
		windows = windows.filter((desktopWindow) => desktopWindow.id !== id);
	}

	function minimizeWindow(id) {
		if (window.innerWidth <= 1023) {
			closeWindow(id);
			return;
		}
		windows = windows.map((desktopWindow) =>
			desktopWindow.id === id ? { ...desktopWindow, minimized: true } : desktopWindow
		);
	}

	function restoreWindow(id) {
		windows = windows.map((desktopWindow) =>
			desktopWindow.id === id ? { ...desktopWindow, minimized: false } : desktopWindow
		);
		focusWindow(id);
	}

	function handleWindowKeydown(event) {
		if (event.key !== 'Escape') return;
		const frontWindow = windows
			.filter((desktopWindow) => !desktopWindow.minimized)
			.sort((a, b) => b.zIndex - a.zIndex)[0];
		if (frontWindow) closeWindow(frontWindow.id);
	}
</script>

<svelte:window onkeydown={handleWindowKeydown} />

<svelte:head>
	<title>Farhan Aulianda</title>
	<meta
		name="description"
		content="Full Stack & Cloud Engineer | Security & AI/ML Enthusiast"
	/>
	<link rel="preload" as="image" href="/images/big-sur-wallpaper.webp" fetchpriority="high" />
</svelte:head>

<!-- Desktop Shell -->
<main class="ios-home" aria-label="Farhan portfolio home screen">
	<div class="ios-status"><span>9:41 <span class="status-location">◢</span></span><span class="status-island"></span><span>▮▮▮ ◉  ▰</span></div>
	<section class="ios-widgets" aria-label="Widgets">
		<WeatherWidget />
		<button class="ios-widget calendar-widget" onclick={() => openWindow('book')}><span class="widget-kicker">WEDNESDAY</span><strong>14</strong><span>3 Events</span><small>Portfolio · Projects · Writing</small></button>
	</section>
	<div class="ios-search">⌕ &nbsp; Search</div>
</main>

<div class="desktop-shell fixed inset-0 isolate transform-gpu overflow-hidden select-none rounded-[24px] border border-white/20 bg-slate-950 shadow-[0_18px_60px_rgba(0,0,0,0.35)]">
	<!-- macOS Big Sur wallpaper -->
	<img
		src="/images/big-sur-wallpaper.webp"
		alt=""
		fetchpriority="high"
		class="absolute inset-0 h-full w-full object-cover object-center"
		aria-hidden="true"
	/>
	<div
		class="pointer-events-none absolute inset-0 bg-gradient-to-b from-slate-950/20 via-slate-950/20 to-slate-950/35"
		aria-hidden="true"
	></div>

	<!-- Menu Bar -->
	<div class="desktop-menubar"><MenuBar bind:brightness /></div>

	<!-- Shared app icons: draggable on desktop, arranged in a grid on touch screens -->
	<div class="desktop-icons absolute inset-0 z-10">
		<!-- Dossier (Resume) -->
		<Icon
			label="Farhan"
			onclick={() => openWindow('dossier')}
			x={iconPositions.dossier.x}
			y={iconPositions.dossier.y}
			onmove={(nx, ny) => moveIcon('dossier', nx, ny)}
		>
			<svg class="w-7 h-7 text-red-400" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24">
				<path stroke-linecap="round" stroke-linejoin="round" d="M19.5 14.25v-2.625a3.375 3.375 0 0 0-3.375-3.375h-1.5A1.125 1.125 0 0 1 13.5 7.125v-1.5a3.375 3.375 0 0 0-3.375-3.375H8.25m2.25 0H5.625c-.621 0-1.125.504-1.125 1.125v17.25c0 .621.504 1.125 1.125 1.125h12.75c.621 0 1.125-.504 1.125-1.125V11.25a9 9 0 0 0-9-9Z" />
			</svg>
		</Icon>

		<!-- Books -->
		<Icon
			label="Books"
			onclick={() => openWindow('book')}
			x={iconPositions.book.x}
			y={iconPositions.book.y}
			onmove={(nx, ny) => moveIcon('book', nx, ny)}
		>
			<svg class="w-7 h-7 text-sky-400" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24">
				<path stroke-linecap="round" stroke-linejoin="round" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5s3.332.477 4.5 1.253v13C19.832 18.477 18.246 18 16.5 18s-3.332.477-4.5 1.253" />
			</svg>
		</Icon>

		<!-- Projects -->
		<Icon
			label="Projects"
			onclick={() => openWindow('projects')}
			x={iconPositions.projects.x}
			y={iconPositions.projects.y}
			onmove={(nx, ny) => moveIcon('projects', nx, ny)}
		>
			<svg class="w-7 h-7 text-amber-400" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24">
				<path stroke-linecap="round" stroke-linejoin="round" d="M2.25 12.75V12A2.25 2.25 0 0 1 4.5 9.75h15A2.25 2.25 0 0 1 21.75 12v.75m-8.69-6.44-2.12-2.12a1.5 1.5 0 0 0-1.061-.44H4.5A2.25 2.25 0 0 0 2.25 6v12a2.25 2.25 0 0 0 2.25 2.25h15A2.25 2.25 0 0 0 21.75 18V9a2.25 2.25 0 0 0-2.25-2.25h-5.379a1.5 1.5 0 0 1-1.06-.44Z" />
			</svg>
		</Icon>

		<!-- Blog -->
		<Icon
			label="Blog"
			onclick={() => openWindow('blog')}
			x={iconPositions.blog.x}
			y={iconPositions.blog.y}
			onmove={(nx, ny) => moveIcon('blog', nx, ny)}
		>
			<svg class="w-7 h-7 text-emerald-400" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24">
				<path stroke-linecap="round" stroke-linejoin="round" d="m16.862 4.487 1.687-1.688a1.875 1.875 0 1 1 2.652 2.652L10.582 16.07a4.5 4.5 0 0 1-1.897 1.13L6 18l.8-2.685a4.5 4.5 0 0 1 1.13-1.897l8.932-8.931Zm0 0L19.5 7.125M18 14v4.75A2.25 2.25 0 0 1 15.75 21H5.25A2.25 2.25 0 0 1 3 18.75V8.25A2.25 2.25 0 0 1 5.25 6H10" />
			</svg>
		</Icon>

		<!-- Spotify -->
		<Icon
			label="Spotify"
			onclick={() => openWindow('spotify')}
			x={iconPositions.spotify.x}
			y={iconPositions.spotify.y}
			onmove={(nx, ny) => moveIcon('spotify', nx, ny)}
		>
			<svg class="w-7 h-7 text-[#1DB954]" viewBox="0 0 24 24" fill="currentColor">
				<path d="M12 0C5.4 0 0 5.4 0 12s5.4 12 12 12 12-5.4 12-12S18.66 0 12 0zm5.521 17.34c-.24.359-.66.48-1.021.24-2.82-1.74-6.36-2.101-10.561-1.141-.418.122-.779-.179-.899-.539-.12-.421.18-.78.54-.9 4.56-1.021 8.52-.6 11.64 1.32.42.18.479.659.301 1.02zm1.44-3.3c-.301.42-.841.6-1.262.3-3.239-1.98-8.159-2.58-11.939-1.38-.479.12-1.02-.12-1.14-.6-.12-.48.12-1.021.6-1.141C9.6 9.9 15 10.561 18.72 12.84c.361.181.54.78.241 1.2zm.12-3.36C15.24 8.4 8.82 8.16 5.16 9.301c-.6.179-1.2-.181-1.38-.721-.18-.601.18-1.2.72-1.381 4.26-1.26 11.28-1.02 15.721 1.621.539.3.719 1.02.419 1.56-.299.421-1.02.599-1.559.3z"/>
			</svg>
		</Icon>

		<!-- Nimbus -->
		<Icon
			label="Nimbus"
			onclick={() => window.open('https://nimbus.farhanaulianda.my.id', '_blank', 'noopener')}
			x={iconPositions.nimbus.x}
			y={iconPositions.nimbus.y}
			onmove={(nx, ny) => moveIcon('nimbus', nx, ny)}
		>
			<!-- Nim, maskot Nimbus -->
			<svg class="w-10 h-10" viewBox="0 0 100 100" fill="none">
				<!-- cloud body -->
				<ellipse cx="50" cy="66" rx="33" ry="20" fill="#fff" />
				<circle cx="33" cy="60" r="15" fill="#fff" />
				<circle cx="50" cy="50" r="20" fill="#fff" />
				<circle cx="67" cy="60" r="15" fill="#fff" />
				<!-- hard hat -->
				<path d="M35 44a15 13 0 0 1 30 0Z" fill="#dd9e36" />
				<rect x="31" y="42" width="38" height="5" rx="2.5" fill="#dd9e36" />
				<rect x="47" y="30" width="6" height="8" rx="3" fill="#c9881f" />
				<!-- eyes -->
				<circle cx="43" cy="56" r="2.8" fill="#16212b" />
				<circle cx="59" cy="56" r="2.8" fill="#16212b" />
				<circle cx="44" cy="55" r="0.9" fill="#fff" />
				<circle cx="60" cy="55" r="0.9" fill="#fff" />
				<!-- cheeks -->
				<circle cx="36" cy="62" r="3" fill="#ffb3a8" opacity="0.6" />
				<circle cx="64" cy="62" r="3" fill="#ffb3a8" opacity="0.6" />
				<!-- mouth -->
				<path d="M43 62q7 8 14 0" stroke="#16212b" stroke-width="2.4" stroke-linecap="round" fill="none" />
			</svg>
		</Icon>
	</div>

	<!-- Dock -->
	<div class="shared-dock">
		<Dock
			onopen={openWindow}
			minimizedWindows={windows.filter((desktopWindow) => desktopWindow.minimized)}
			onrestore={restoreWindow}
		/>
	</div>

	<!-- Brightness overlay -->
	{#if brightness < 100}
		<div
			class="absolute inset-0 bg-black pointer-events-none z-[950]"
			style="opacity: {(100 - brightness) / 100}"
		></div>
	{/if}
	{#if windowLimitNotice}
		<div class="liquid-glass-popover fixed top-12 left-1/2 z-[1100] -translate-x-1/2 rounded-xl px-4 py-2 text-xs font-medium text-slate-900 shadow-xl" role="status">
			Batas 8 jendela tercapai. Tutup jendela untuk membuka yang baru.
		</div>
	{/if}
	{#each windows.filter((desktopWindow) => !desktopWindow.minimized) as desktopWindow (desktopWindow.id)}
		<DesktopWindow
			title={desktopWindow.title}
			zIndex={desktopWindow.zIndex}
			initialX={desktopWindow.x}
			initialY={desktopWindow.y}
			initialWidth={desktopWindow.width}
			initialHeight={desktopWindow.height}
			close={() => closeWindow(desktopWindow.id)}
			minimize={() => minimizeWindow(desktopWindow.id)}
			onfocus={() => focusWindow(desktopWindow.id)}
			onnavigate={(name) => navigateWindow(desktopWindow.id, name)}
		>
			{#snippet children(search)}
				{#if desktopWindow.name === 'projects'}
					<ProjectsContent {search} />
				{:else if desktopWindow.name === 'blog'}
					<BlogContent {posts} {search} />
				{:else if desktopWindow.name === 'book'}
					<BooksContent books={data?.books || []} {search} />
				{:else if desktopWindow.name === 'spotify'}
					<SpotifyContent {search} />
				{:else if desktopWindow.name === 'dossier'}
					<div class="min-h-full bg-white px-5 py-4 text-zinc-900">
						<a href="/resume.pdf" download="Resume - Farhan Aulianda.pdf" class="mb-4 inline-flex items-center gap-2 rounded-lg bg-zinc-900 px-3 py-2 text-xs font-semibold text-white hover:bg-zinc-700">
							Download PDF
						</a>
						<ResumeContent />
					</div>
				{/if}
			{/snippet}
		</DesktopWindow>
	{/each}
</div>

<style>
	.ios-home { display: none; }
	@media (max-width: 1023px) {
		.ios-home {
			position: fixed; inset: 0; z-index: 0; display: flex; flex-direction: column;
			overflow: auto; padding: max(env(safe-area-inset-top), 14px) 18px max(env(safe-area-inset-bottom), 18px);
			color: white; background: #080b12 url('/images/big-sur-wallpaper.webp') center / cover;
			font-family: -apple-system, BlinkMacSystemFont, 'SF Pro Display', sans-serif;
		}
		.ios-home::before { content: ''; position: fixed; inset: 0; background: linear-gradient(180deg, #07101c33, #06101c45); pointer-events: none; }
		.ios-home > * { position: relative; }
		.desktop-shell { display: block; border: 0; border-radius: 0; background: transparent; box-shadow: none; overflow: visible; }
		.desktop-shell > img, .desktop-shell > .pointer-events-none { display: none; }
		.desktop-menubar { display: none; }
		.desktop-icons { position: fixed !important; inset: auto 18px auto !important; top: clamp(270px, 40vh, 560px) !important; display: grid; grid-template-columns: repeat(4, minmax(0, 1fr)); gap: clamp(16px, 3.8vh, 30px) 6px; }
		.ios-status { height: 36px; display: flex; align-items: center; justify-content: space-between; padding: 0 8px; font-size: 15px; font-weight: 650; }
		.status-island { width: 70px; height: 22px; border-radius: 20px; background: #050505; }
		.status-location { font-size: 12px; }
		.ios-widgets { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin: clamp(22px, 5vh, 44px) 0 30px; }
		:global(.ios-widget) { aspect-ratio: 1; border: 1px solid #ffffff2b; border-radius: 25%; padding: 14px; display: flex; flex-direction: column; justify-content: center; text-align: left; color: white; background: linear-gradient(145deg,#91a6c1d9,#43556edb); box-shadow: inset 0 1px #ffffff55; overflow: hidden; font: inherit; cursor: pointer; }
		:global(.weather-widget) { background: linear-gradient(145deg,#91a6c1d9,#43556edb); }
		:global(.calendar-widget) { background: linear-gradient(145deg,#282a2edc,#101114e8); }
		:global(.widget-kicker) { font-size: clamp(11px, 2vw, 17px); font-weight: 650; white-space: nowrap; }
		:global(.calendar-widget .widget-kicker) { color: #ff5b62; }
		:global(.ios-widget strong) { font-size: clamp(40px, 10vw, 78px); line-height: 1.08; font-weight: 300; }
		:global(.calendar-widget strong) { font-size: clamp(42px, 10vw, 78px); }
		:global(.ios-widget > span:not(.widget-kicker)) { margin-top: 7px; font-size: clamp(13px, 3vw, 19px); }
		:global(.ios-widget small) { margin-top: 4px; color: #ffffffb5; font-size: clamp(9px, 2.1vw, 13px); white-space: nowrap; }
		.ios-search { flex: 0 0 auto; align-self: center; margin: auto 0 20px; padding: 7px 17px; border: 1px solid #ffffff30; border-radius: 22px; background: #ffffff30; backdrop-filter: blur(18px); color: #ffffffe0; font-size: 14px; }
	}
	@media (max-width: 699px) {
		.ios-status { display: none; }
	}
	@media (min-width: 700px) and (max-width: 1023px) {
		.ios-home { padding-left: clamp(38px, 9vw, 120px); padding-right: clamp(38px, 9vw, 120px); }
		.ios-widgets { max-width: 620px; width: 100%; align-self: center; margin-top: 7vh; gap: 22px; }
		.desktop-icons { left: 50% !important; right: auto !important; width: min(760px, calc(100vw - 120px)); transform: translateX(-50%); grid-template-columns: repeat(6, minmax(0,1fr)); gap: 34px 12px; }
	}
</style>
