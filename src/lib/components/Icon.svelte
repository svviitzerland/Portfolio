<script>
	import { fly } from 'svelte/transition';
	import AppIcon from '$lib/components/AppIcon.svelte';

	let { label, onclick, x = 0, y = 0, onmove } = $props();

	let dragging = $state(false);
	let dragOffsetX = $state(0);
	let dragOffsetY = $state(0);
	let startPointer = { x: 0, y: 0 };
	let hasMoved = false;

	const DRAG_THRESHOLD = 5;
	const GRID_SIZE = 20;

	// During drag, show dragged position; otherwise show prop position
	let displayX = $derived(dragging ? x + dragOffsetX : x);
	let displayY = $derived(dragging ? y + dragOffsetY : y);

	function snapToGrid(value) {
		return Math.round(value / GRID_SIZE) * GRID_SIZE;
	}

	function onPointerDown(e) {
		if (e.button !== 0) return;
		if (window.innerWidth <= 1023) return;

		dragging = true;
		hasMoved = false;
		dragOffsetX = 0;
		dragOffsetY = 0;
		startPointer = { x: e.clientX, y: e.clientY };

		e.currentTarget.setPointerCapture(e.pointerId);
		e.preventDefault();
	}

	function onPointerMove(e) {
		if (!dragging) return;

		const dx = e.clientX - startPointer.x;
		const dy = e.clientY - startPointer.y;

		if (!hasMoved && Math.abs(dx) < DRAG_THRESHOLD && Math.abs(dy) < DRAG_THRESHOLD) {
			return;
		}

		hasMoved = true;
		dragOffsetX = dx;
		dragOffsetY = dy;
	}

	function onPointerUp(e) {
		if (!dragging) return;
		dragging = false;

		if (hasMoved) {
			const finalX = snapToGrid(x + dragOffsetX);
			const finalY = snapToGrid(y + dragOffsetY);
			onmove?.(finalX, finalY);
		} else {
			e.stopPropagation();
			onclick?.();
		}

		dragOffsetX = 0;
		dragOffsetY = 0;
	}

	function onButtonClick() {
		if (window.innerWidth <= 1023) onclick?.();
	}
</script>

<div
	class="app-icon app-icon-{label?.toLowerCase()} absolute"
	style="left: {displayX}px; top: {displayY}px; z-index: {dragging ? 50 : 10};"
	in:fly={{ y: 10, duration: 300 }}
>
	<button
		class="app-icon-button group flex flex-col items-center gap-1.5 w-[76px] p-2 rounded-lg hover:bg-white/10 transition-all duration-200 cursor-default select-none"
		style="touch-action: none;"
		onclick={onButtonClick}
		onpointerdown={onPointerDown}
		onpointermove={onPointerMove}
		onpointerup={onPointerUp}
		onpointercancel={onPointerUp}
	>
		<!-- Icon container -->
		<div
			class="app-icon-art w-[68px] h-[68px] flex items-center justify-center rounded-[19px] group-hover:scale-105 transition-all duration-200"
		>
			<AppIcon name={label} />
		</div>

		<span
			class="text-[11px] text-white/90 text-center leading-tight font-medium max-w-full truncate"
			style="text-shadow: 0 1px 4px rgba(0,0,0,0.9), 0 0 10px rgba(0,0,0,0.5);"
		>
			{label}
		</span>
	</button>
</div>

<style>
	@media (max-width: 1023px) {
		.app-icon {
			position: static !important;
			left: auto !important;
			top: auto !important;
			width: 100%;
		}
		.app-icon-button {
			width: 100%;
			padding: 0;
			gap: 7px;
			touch-action: manipulation !important;
		}
		.app-icon-button > div {
			width: min(17vw, 68px);
			height: min(17vw, 68px);
			border-radius: 24%;
		}
		.app-icon-button > div :global(.app-art) {
			width: 100%;
			height: 100%;
		}
		.app-icon-button > span {
			font-size: clamp(11px, 2.7vw, 14px);
		}
	}
	@media (min-width: 700px) and (max-width: 1023px) {
		.app-icon-button > div { width: 72px; height: 72px; }
	}
	@media (max-width: 699px) {
		.app-icon-farhan,
		.app-icon-projects,
		.app-icon-blog,
		.app-icon-nimbus { display: none !important; }
	}
	.app-icon-art { background: transparent; border: 0; box-shadow: none; backdrop-filter: none; }
</style>
