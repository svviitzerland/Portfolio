<script>
	let { books = [], search = '' } = $props();

	const filtered = $derived(
		search
			? books.filter((book) =>
					[book.title, book.author].some((value) => value?.toLowerCase().includes(search.toLowerCase()))
				)
			: books
	);

	function progress(book) {
		if (!book.totalPages) return 0;
		return Math.min(100, Math.max(0, Math.round((book.pagesRead / book.totalPages) * 100)));
	}
</script>

<div class="p-6 md:p-10 text-zinc-100">
	<div class="space-y-3">
		{#each filtered as book (book.title)}
			<article class="flex gap-5 rounded-xl border border-zinc-800/80 bg-zinc-900/40 p-4 md:gap-6 md:p-5">
				{#if book.cover}
					<img src={book.cover} alt="Cover of {book.title}" class="h-32 w-[5.5rem] shrink-0 rounded-md object-cover shadow-lg md:h-40 md:w-28" />
				{:else}
					<div class="flex h-32 w-[5.5rem] shrink-0 items-center justify-center rounded-md border border-zinc-700 bg-gradient-to-br from-zinc-700 to-zinc-950 p-2 text-center text-xs font-semibold text-zinc-300 shadow-lg md:h-40 md:w-28">
						{book.title}
					</div>
				{/if}
				<div class="flex min-w-0 flex-1 flex-col justify-center gap-2">
					<div>
						<h2 class="text-base font-semibold text-zinc-100 md:text-lg">{book.title}</h2>
						<p class="mt-1 text-sm text-zinc-400">{book.author}</p>
					</div>
					<div class="mt-2">
						<div class="mb-2 flex items-center justify-between gap-3 text-xs text-zinc-500">
							<span>{book.pagesRead} of {book.totalPages} pages</span>
							<span class="font-mono text-zinc-300">{progress(book)}%</span>
						</div>
						<div class="h-1.5 overflow-hidden rounded-full bg-zinc-800" role="progressbar" aria-label="Reading progress for {book.title}" aria-valuenow={progress(book)} aria-valuemin="0" aria-valuemax="100">
							<div class="h-full rounded-full bg-sky-400 transition-[width]" style="width: {progress(book)}%"></div>
						</div>
					</div>
				</div>
			</article>
		{:else}
			<div class="py-20 text-center text-zinc-500">
				<p class="text-sm">{search ? `No books found for "${search}"` : 'No books added yet.'}</p>
			</div>
		{/each}
	</div>
</div>
