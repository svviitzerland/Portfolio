<script>
	import { onMount } from 'svelte';

	let { brightness = $bindable(100), lockscreen = false } = $props();

	let currentTime = $state('');
	let showBrightnessSlider = $state(false);

	function updateTime() {
		const now = new Date();
		currentTime = new Intl.DateTimeFormat('en-US', {
			weekday: 'short',
			month: 'short',
			day: 'numeric',
			hour: 'numeric',
			minute: '2-digit',
			hour12: true
		}).format(now);
	}

	onMount(() => {
		updateTime();
		const interval = setInterval(updateTime, 30000);
		return () => clearInterval(interval);
	});

	function toggleBrightness() {
		showBrightnessSlider = !showBrightnessSlider;
	}

	function handleClickOutside(e) {
		if (showBrightnessSlider && !e.target.closest('.brightness-panel')) {
			showBrightnessSlider = false;
		}
	}
</script>

<svelte:window onclick={handleClickOutside} />

<header
	class="liquid-glass-menubar fixed top-0 left-0 right-0 z-[1000] flex h-8 items-center justify-between px-4 select-none"
	class:lockscreen-menubar={lockscreen}
>
	<!-- Left: Name -->
	<span class={lockscreen ? 'text-[12px] font-medium text-white/90' : 'text-[12px] font-medium text-slate-950/85'}>Farhan Aulianda</span>

	<!-- Right: Status icons + Time -->
	<div class="flex items-center gap-3">
		<!-- Brightness -->
		<div class="relative brightness-panel">
			<button
				onclick={(e) => { e.stopPropagation(); toggleBrightness(); }}
				class={lockscreen ? 'text-white/85 hover:text-white transition-colors' : 'text-slate-900/70 hover:text-slate-950 transition-colors'}
				aria-label="Brightness"
			>
				<svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
					<path stroke-linecap="round" stroke-linejoin="round" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
				</svg>
			</button>

			<!-- Brightness slider dropdown -->
			{#if showBrightnessSlider}
				<!-- svelte-ignore a11y_click_events_have_key_events a11y_no_static_element_interactions -->
				<div
					class="liquid-glass-popover absolute top-8 right-0 w-48 rounded-xl p-3"
					role="presentation"
					onclick={(e) => e.stopPropagation()}
					onkeydown={(e) => e.stopPropagation()}
				>
					<div class="flex items-center gap-2">
						<svg class="w-3 h-3 text-slate-900/55 shrink-0" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
							<path stroke-linecap="round" stroke-linejoin="round" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
						</svg>
						<input
							type="range"
							min="20"
							max="100"
							bind:value={brightness}
							class="w-full h-1 bg-slate-900/20 rounded-full appearance-none cursor-pointer accent-slate-800"
						/>
						<svg class="w-4 h-4 text-slate-900/80 shrink-0" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
							<path stroke-linecap="round" stroke-linejoin="round" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z" />
						</svg>
					</div>
				</div>
			{/if}
		</div>

		<!-- WiFi -->
		<svg class={lockscreen ? 'w-3.5 h-3.5 text-white/85' : 'w-3.5 h-3.5 text-slate-900/70'} fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
			<path stroke-linecap="round" stroke-linejoin="round" d="M8.288 15.038a5.25 5.25 0 017.424 0M5.106 11.856c3.807-3.808 9.98-3.808 13.788 0M1.924 8.674c5.565-5.565 14.587-5.565 20.152 0M12 18.75h.007v.008H12v-.008z" />
		</svg>

		<!-- Battery -->
		<div class="flex items-center gap-0.5">
			<div class={lockscreen ? 'w-5 h-2.5 border border-white/70 rounded-sm relative p-[1px]' : 'w-5 h-2.5 border border-slate-900/60 rounded-sm relative p-[1px]'}>
				<div class={lockscreen ? 'h-full bg-white/85 rounded-[1px]' : 'h-full bg-slate-900/75 rounded-[1px]'} style="width: 80%"></div>
			</div>
			<div class={lockscreen ? 'w-[2px] h-1.5 bg-white/70 rounded-r-sm' : 'w-[2px] h-1.5 bg-slate-900/60 rounded-r-sm'}></div>
		</div>

		<!-- Separator -->
		<div class={lockscreen ? 'w-px h-3 bg-white/30' : 'w-px h-3 bg-slate-900/20'}></div>

		<!-- Time -->
		<span class={lockscreen ? 'text-[12px] text-white/85 tabular-nums' : 'text-[12px] text-slate-900/75 tabular-nums'}>{currentTime}</span>
	</div>
</header>

<style>
	:global(.liquid-glass-menubar.lockscreen-menubar) {
		background: transparent;
		backdrop-filter: none;
		-webkit-backdrop-filter: none;
		border-bottom: 0;
		box-shadow: none;
	}

	input[type='range']::-webkit-slider-thumb {
		-webkit-appearance: none;
		width: 12px;
		height: 12px;
		border-radius: 50%;
		background: #1e293b;
		cursor: pointer;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.4);
	}

	input[type='range']::-moz-range-thumb {
		width: 12px;
		height: 12px;
		border-radius: 50%;
		background: #1e293b;
		cursor: pointer;
		border: none;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.4);
	}
</style>
