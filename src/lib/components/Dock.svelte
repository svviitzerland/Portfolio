<script>
	import cv from '$data/cv.json';
	import AppIcon from '$lib/components/AppIcon.svelte';

	let { onopen, minimizedWindows = [], onrestore } = $props();

	const { socials } = cv;

	const iconMap = {
		GitHub: {
			viewBox: '0 0 24 24',
			path: 'M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z'
		},
		LinkedIn: {
			viewBox: '0 0 24 24',
			path: 'M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433c-1.144 0-2.063-.926-2.063-2.065 0-1.138.92-2.063 2.063-2.063 1.14 0 2.064.925 2.064 2.063 0 1.139-.925 2.065-2.064 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z'
		},
		Blog: {
			viewBox: '0 0 24 24',
			path: 'M19 20H5a2 2 0 01-2-2V6a2 2 0 012-2h10a2 2 0 012 2v1h2a2 2 0 012 2v9a2 2 0 01-2 2zM5 6v12h14V9h-4V6H5zm2 3h6v2H7V9zm0 4h10v2H7v-2zm0 4h10v2H7v-2z'
		},
		Medium: {
			viewBox: '0 0 24 24',
			path: 'M13.54 12a6.8 6.8 0 01-6.77 6.82A6.8 6.8 0 010 12a6.8 6.8 0 016.77-6.82A6.8 6.8 0 0113.54 12zM20.96 12c0 3.54-1.51 6.42-3.38 6.42-1.87 0-3.39-2.88-3.39-6.42s1.52-6.42 3.39-6.42 3.38 2.88 3.38 6.42M24 12c0 3.17-.53 5.75-1.19 5.75-.66 0-1.19-2.58-1.19-5.75s.53-5.75 1.19-5.75C23.47 6.25 24 8.83 24 12z'
		},
		Instagram: {
			viewBox: '0 0 24 24',
			path: 'M12 2.163c3.204 0 3.584.012 4.85.07 3.252.148 4.771 1.691 4.919 4.919.058 1.265.069 1.645.069 4.849 0 3.205-.012 3.584-.069 4.849-.149 3.225-1.664 4.771-4.919 4.919-1.266.058-1.644.07-4.85.07-3.204 0-3.584-.012-4.849-.07-3.26-.149-4.771-1.699-4.919-4.92-.058-1.265-.07-1.644-.07-4.849 0-3.204.013-3.583.07-4.849.149-3.227 1.664-4.771 4.919-4.919 1.266-.057 1.645-.069 4.849-.069zM12 0C8.741 0 8.333.014 7.053.072 2.695.272.273 2.69.073 7.052.014 8.333 0 8.741 0 12c0 3.259.014 3.668.072 4.948.2 4.358 2.618 6.78 6.98 6.98C8.333 23.986 8.741 24 12 24c3.259 0 3.668-.014 4.948-.072 4.354-.2 6.782-2.618 6.979-6.98.059-1.28.073-1.689.073-4.948 0-3.259-.014-3.667-.072-4.947-.196-4.354-2.617-6.78-6.979-6.98C15.668.014 15.259 0 12 0zm0 5.838a6.162 6.162 0 100 12.324 6.162 6.162 0 000-12.324zM12 16a4 4 0 110-8 4 4 0 010 8zm6.406-11.845a1.44 1.44 0 100 2.881 1.44 1.44 0 000-2.881z'
		},
		Spotify: {
			viewBox: '0 0 24 24',
			path: 'M12 0C5.4 0 0 5.4 0 12s5.4 12 12 12 12-5.4 12-12S18.66 0 12 0zm5.521 17.34c-.24.359-.66.48-1.021.24-2.82-1.74-6.36-2.101-10.561-1.141-.418.122-.779-.179-.899-.539-.12-.421.18-.78.54-.9 4.56-1.021 8.52-.6 11.64 1.32.42.18.479.659.301 1.02zm1.44-3.3c-.301.42-.841.6-1.262.3-3.239-1.98-8.159-2.58-11.939-1.38-.479.12-1.02-.12-1.14-.6-.12-.48.12-1.021.6-1.141C9.6 9.9 15 10.561 18.72 12.84c.361.181.54.78.241 1.2zm.12-3.36C15.24 8.4 8.82 8.16 5.16 9.301c-.6.179-1.2-.181-1.38-.721-.18-.601.18-1.2.72-1.381 4.26-1.26 11.28-1.02 15.721 1.621.539.3.719 1.02.419 1.56-.299.421-1.02.599-1.559.3z'
		}
	};

	let tooltipIndex = $state(-1);
</script>

<!-- Dock -->
<div class="dock-position pointer-events-none fixed inset-x-0 bottom-2 z-[900] flex justify-center px-2 pb-[env(safe-area-inset-bottom)] sm:bottom-3 sm:px-4">
	<nav
		class="desktop-dock dock-glass liquid-glass-surface pointer-events-auto flex max-w-[calc(100vw-1rem)] items-end gap-0.5 overflow-x-auto rounded-[1.1rem] px-1.5 py-1 sm:gap-1 sm:px-2"
		aria-label="Quick links dock"
	>
		<div class="dock-entry dock-finder relative">
			<button
				type="button"
				class="dock-item liquid-glass-hover flex h-9 w-9 cursor-pointer items-center justify-center rounded-[0.8rem] sm:h-10 sm:w-10"
				onclick={(e) => { e.stopPropagation(); onopen('projects'); }}
				aria-label="Open Finder Projects"
			>
				<img class="dock-projects-finder h-8 w-8 rounded-[9px] sm:h-9 sm:w-9" src="/images/finder-icon.png" alt="" />
				<img class="dock-projects-files h-8 w-8 sm:h-9 sm:w-9" src="/images/files-folder-icon.png" alt="" />
			</button>
		</div>

		{#each socials as social, i (social.network)}
			<div class="dock-entry dock-social-{social.network.toLowerCase()} relative">
				<!-- Tooltip -->
				{#if tooltipIndex === i}
					<div
						class="dock-tooltip liquid-glass-popover pointer-events-none absolute -top-10 left-1/2 -translate-x-1/2 whitespace-nowrap rounded-lg px-3 py-1 text-xs text-white shadow-xl"
					>
						{social.network}
						<div class="dock-tooltip-arrow absolute -bottom-1 left-1/2 h-2 w-2 -translate-x-1/2 rotate-45"></div>
					</div>
				{/if}

				{#if social.network === 'Blog'}
					<button
						type="button"
						class="dock-item liquid-glass-hover flex h-9 w-9 cursor-pointer items-center justify-center rounded-[0.8rem] text-white sm:h-10 sm:w-10"
						onmouseenter={() => (tooltipIndex = i)}
						onmouseleave={() => (tooltipIndex = -1)}
						onclick={(e) => { e.stopPropagation(); onopen('blog'); }}
						aria-label="Open Blog"
					>
						<AppIcon name="Blog" class="h-9 w-9" />
					</button>
				{:else}
					<a
						href={social.url}
						target="_blank"
						rel="noopener noreferrer"
						class="dock-item liquid-glass-hover flex h-9 w-9 items-center justify-center rounded-[0.8rem] text-white sm:h-10 sm:w-10"
						onmouseenter={() => (tooltipIndex = i)}
						onmouseleave={() => (tooltipIndex = -1)}
						aria-label={social.network}
					>
						{#if iconMap[social.network]}
							<svg class="w-6 h-6" viewBox={iconMap[social.network].viewBox} fill="currentColor">
								<path d={iconMap[social.network].path} />
							</svg>
						{:else}
							<span class="text-lg font-bold">{social.network[0]}</span>
						{/if}
					</a>
				{/if}
			</div>
		{/each}

		<div class="dock-separator self-center" aria-hidden="true"></div>

		<!-- Books dock button -->
		<div class="dock-entry dock-book relative">
			{#if tooltipIndex === socials.length + 1}
				<div class="dock-tooltip liquid-glass-popover pointer-events-none absolute -top-10 left-1/2 -translate-x-1/2 whitespace-nowrap rounded-lg px-3 py-1 text-xs text-white shadow-xl">
					Books
					<div class="dock-tooltip-arrow absolute -bottom-1 left-1/2 h-2 w-2 -translate-x-1/2 rotate-45"></div>
				</div>
			{/if}
			<button
				type="button"
				class="dock-item liquid-glass-hover flex h-9 w-9 cursor-pointer items-center justify-center rounded-[0.8rem] text-sky-100 sm:h-10 sm:w-10"
				onmouseenter={() => (tooltipIndex = socials.length + 1)}
				onmouseleave={() => (tooltipIndex = -1)}
				onclick={(e) => { e.stopPropagation(); onopen('book'); }}
				aria-label="Open Books"
			>
				<AppIcon name="Books" class="h-9 w-9" />
			</button>
		</div>

		<!-- Spotify dock button -->
		<div class="dock-entry dock-spotify relative">
			{#if tooltipIndex === socials.length}
				<div class="dock-tooltip liquid-glass-popover pointer-events-none absolute -top-10 left-1/2 -translate-x-1/2 whitespace-nowrap rounded-lg px-3 py-1 text-xs text-white shadow-xl">
					Spotify
					<div class="dock-tooltip-arrow absolute -bottom-1 left-1/2 h-2 w-2 -translate-x-1/2 rotate-45"></div>
				</div>
			{/if}
			<button
				type="button"
				class="dock-item liquid-glass-hover flex h-9 w-9 cursor-pointer items-center justify-center rounded-[0.8rem] text-[#1ed760] sm:h-10 sm:w-10"
				onmouseenter={() => (tooltipIndex = socials.length)}
				onmouseleave={() => (tooltipIndex = -1)}
				onclick={(e) => { e.stopPropagation(); onopen('spotify'); }}
				aria-label="Open Spotify"
			>
				<AppIcon name="Spotify" class="h-9 w-9" />
			</button>
		</div>

		{#each minimizedWindows as desktopWindow, i (desktopWindow.id)}
			<div class="dock-entry dock-window relative shrink-0">
				{#if tooltipIndex === socials.length + i + 2}
					<div class="dock-tooltip liquid-glass-popover pointer-events-none absolute -top-10 left-1/2 -translate-x-1/2 rounded-lg px-3 py-1 text-xs text-white shadow-xl">
						{desktopWindow.title}
						<div class="dock-tooltip-arrow absolute -bottom-1 left-1/2 h-2 w-2 -translate-x-1/2 rotate-45"></div>
					</div>
				{/if}
				<button
					type="button"
					class="dock-item liquid-glass-hover flex h-9 w-9 cursor-pointer items-center justify-center rounded-[0.8rem] sm:h-10 sm:w-10"
					onmouseenter={() => (tooltipIndex = socials.length + i + 2)}
					onmouseleave={() => (tooltipIndex = -1)}
					onclick={(event) => { event.stopPropagation(); onrestore(desktopWindow.id); }}
					aria-label="Restore {desktopWindow.title}"
				>
					<span class="dock-window-preview" aria-hidden="true"><span></span></span>
				</button>
			</div>
		{/each}
	</nav>

	<nav class="mobile-dock liquid-glass-surface pointer-events-auto flex items-center justify-around gap-2 rounded-[28px] px-3 py-2" aria-label="Quick apps">
		<button type="button" class="dock-item flex h-14 w-14 items-center justify-center" onclick={(e) => { e.stopPropagation(); onopen('dossier'); }} aria-label="Open Farhan resume">
			<AppIcon name="Farhan" class="h-12 w-12" />
		</button>
		<button type="button" class="dock-item flex h-14 w-14 items-center justify-center" onclick={(e) => { e.stopPropagation(); onopen('projects'); }} aria-label="Open Projects">
			<AppIcon name="Projects" class="h-12 w-12" />
		</button>
		<button type="button" class="dock-item flex h-14 w-14 items-center justify-center" onclick={(e) => { e.stopPropagation(); onopen('blog'); }} aria-label="Open Blog">
			<AppIcon name="Blog" class="h-12 w-12" />
		</button>
		<button type="button" class="dock-item flex h-14 w-14 items-center justify-center" onclick={(e) => { e.stopPropagation(); window.open('https://nimbus.farhanaulianda.my.id', '_blank', 'noopener'); }} aria-label="Open Nimbus">
			<AppIcon name="Nimbus" class="h-12 w-12" />
		</button>
	</nav>
</div>

<style>
	.dock-glass {
		min-height: 2.75rem;
		scrollbar-width: none;
	}

	.mobile-dock { display: none; }

	.dock-projects-files { display: none; }

	.dock-glass::-webkit-scrollbar {
		display: none;
	}

	.dock-item {
		transition:
			transform 180ms cubic-bezier(0.2, 0.8, 0.2, 1),
			background-color 180ms ease,
			filter 180ms ease;
		-webkit-tap-highlight-color: transparent;
	}

	.dock-item:hover,
	.dock-item:focus-visible {
		transform: translateY(-3px) scale(1.1);
		filter: drop-shadow(0 3px 5px rgb(0 0 0 / 24%));
	}

	.dock-item:active {
		transform: translateY(-2px) scale(0.96);
	}

	.dock-tooltip {
		white-space: nowrap;
	}

	.dock-tooltip-arrow {
		background: rgb(var(--liquid-glass-tint) / var(--liquid-glass-popover-opacity));
		border-right: 1px solid rgb(var(--liquid-glass-tint) / var(--liquid-glass-edge-opacity));
		border-bottom: 1px solid rgb(var(--liquid-glass-tint) / var(--liquid-glass-edge-opacity));
	}

	.dock-separator {
		width: 1px;
		height: 1.6rem;
		margin-inline: 0.15rem;
		background: rgb(var(--liquid-glass-tint) / var(--liquid-glass-separator-opacity));
	}

	.dock-window-preview {
		position: relative;
		display: block;
		width: 25px;
		height: 20px;
		overflow: hidden;
		border: 1px solid rgb(255 255 255 / 55%);
		border-radius: 4px;
		background: linear-gradient(145deg, rgb(240 246 255 / 72%), rgb(89 123 161 / 76%));
		box-shadow: 0 2px 4px rgb(0 0 0 / 22%);
	}

	.dock-window-preview::before {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		height: 4px;
		content: '';
		background: rgb(28 35 45 / 80%);
	}

	.dock-window-preview span {
		position: absolute;
		top: 7px;
		left: 4px;
		width: 15px;
		height: 8px;
		border-radius: 2px;
		background: rgb(255 255 255 / 72%);
	}

	@media (max-width: 1023px) {
		.dock-position {
			bottom: 10px;
			padding-inline: 18px;
		}

		.dock-glass {
			width: min(100%, 560px);
			max-width: none;
			min-height: 90px;
			align-items: center;
			justify-content: space-around;
			gap: 8px;
			padding: 12px 16px;
			border-radius: 28px;
		}

		.dock-item {
			width: 64px !important;
			height: 64px !important;
			border-radius: 20px !important;
		}

		.dock-item > :global(svg) {
			width: 44px;
			height: 44px;
		}

		.dock-item img {
			width: 44px;
			height: 44px;
		}

		.dock-social-linkedin,
		.dock-social-instagram,
		.dock-social-medium,
		.dock-separator,
		.dock-window {
			display: none;
		}
	}

	@media (max-width: 699px) {
		.desktop-dock { display: none; }
		.mobile-dock { display: flex; width: min(100%, 380px); min-height: 76px; }
		.mobile-dock .dock-item { width: 56px !important; height: 56px !important; padding: 0; }
		.mobile-dock .dock-item > :global(svg) { width: 48px; height: 48px; }
		.mobile-dock :global(.app-art.app-art-farhan),
		.mobile-dock :global(.app-art.app-art-nimbus) { transform: none; }
	}

	@media (prefers-reduced-motion: reduce) {
		.dock-item {
			transition: none;
		}

		.dock-item:hover,
		.dock-item:focus-visible,
		.dock-item:active {
			transform: none;
		}
	}
</style>
