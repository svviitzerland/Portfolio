<script>
	let { name, class: className = '' } = $props();
</script>

<svg class="app-art app-art-{name?.toLowerCase()} {className}" viewBox="0 0 80 80" role="img" aria-label={name}>
	{#if name === 'Projects'}
		<image class="finder-image" href="/images/finder-icon.png" width="80" height="80" preserveAspectRatio="xMidYMid meet"/>
		<image class="files-image" href="/images/files-folder-icon.png" x="8" y="8" width="64" height="64" preserveAspectRatio="xMidYMid meet"/>
	{:else if name === 'Farhan'}
		<rect width="80" height="80" rx="19" fill="#28313D"/>
		<image href="/images/lockscreen-avatar.webp" x="5" y="5" width="70" height="70" preserveAspectRatio="xMidYMid slice" clip-path="inset(0 round 16px)"/>
	{:else if name === 'Blog'}
		<image href="/images/notes-macos-icon.png" width="80" height="80" preserveAspectRatio="xMidYMid meet"/>
	{:else if name === 'Books'}
		<image href="/images/books-macos-icon.png" width="80" height="80" preserveAspectRatio="xMidYMid meet"/>
	{:else if name === 'Spotify'}
		<rect width="80" height="80" rx="19" fill="#101114"/>
		<svg x="10" y="10" width="60" height="60" viewBox="0 0 24 24">
			<path fill="#1ED760" d="M12 0C5.4 0 0 5.4 0 12s5.4 12 12 12 12-5.4 12-12S18.66 0 12 0zm5.521 17.34c-.24.359-.66.48-1.021.24-2.82-1.74-6.36-2.101-10.561-1.141-.418.122-.779-.179-.899-.539-.12-.421.18-.78.54-.9 4.56-1.021 8.52-.6 11.64 1.32.42.18.479.659.301 1.02zm1.44-3.3c-.301.42-.841.6-1.262.3-3.239-1.98-8.159-2.58-11.939-1.38-.479.12-1.02-.12-1.14-.6-.12-.48.12-1.021.6-1.141C9.6 9.9 15 10.561 18.72 12.84c.361.181.54.78.241 1.2zm.12-3.36C15.24 8.4 8.82 8.16 5.16 9.301c-.6.179-1.2-.181-1.38-.721-.18-.601.18-1.2.72-1.381 4.26-1.26 11.28-1.02 15.721 1.621.539.3.719 1.02.419 1.56-.299.421-1.02.599-1.559.3z"/>
		</svg>
	{:else if name === 'Nimbus'}
		<rect width="80" height="80" rx="19" fill="#E8EDF2"/>
		<svg x="-10" y="-10" width="100" height="100" viewBox="0 0 100 100" fill="none">
			<g transform="translate(0 -8)" style="filter: drop-shadow(0 1.5px 1px #aab8c6)">
				<ellipse cx="50" cy="66" rx="33" ry="20" fill="#fff"/>
				<circle cx="33" cy="60" r="15" fill="#fff"/>
				<circle cx="50" cy="50" r="20" fill="#fff"/>
				<circle cx="67" cy="60" r="15" fill="#fff"/>
				<path d="M35 44a15 13 0 0 1 30 0Z" fill="#dd9e36"/>
				<rect x="31" y="42" width="38" height="5" rx="2.5" fill="#dd9e36"/>
				<rect x="47" y="30" width="6" height="8" rx="3" fill="#c9881f"/>
				<circle cx="43" cy="56" r="2.8" fill="#16212b"/>
				<circle cx="59" cy="56" r="2.8" fill="#16212b"/>
				<circle cx="44" cy="55" r=".9" fill="#fff"/>
				<circle cx="60" cy="55" r=".9" fill="#fff"/>
				<circle cx="36" cy="62" r="3" fill="#ffb3a8" opacity=".6"/>
				<circle cx="64" cy="62" r="3" fill="#ffb3a8" opacity=".6"/>
				<path d="M43 62q7 8 14 0" stroke="#16212b" stroke-width="2.4" stroke-linecap="round"/>
			</g>
		</svg>
	{:else}
		<rect width="80" height="80" rx="19" fill="#426AAB"/><text x="40" y="53" text-anchor="middle" fill="white" font-size="36" font-weight="700">{name?.[0]}</text>
	{/if}
	{#if !['Spotify', 'Projects', 'Blog', 'Books'].includes(name)}
		<path d="M19 1h42" stroke="#8B99A9" stroke-opacity=".25" stroke-width="1.5" stroke-linecap="round"/>
	{/if}
</svg>

<style>
	.app-art { display: block; width: 100%; height: 100%; overflow: visible; filter: drop-shadow(0 4px 7px rgb(0 0 0 / 22%)); }
	.app-art:not(.app-art-projects) { transform: scale(.82); }
	.app-art.app-art-blog, .app-art.app-art-books { transform: none; }
	.files-image { display: none; }
	@media (max-width: 699px) {
		.app-art-projects .finder-image { display: none; }
		.app-art-projects .files-image { display: block; }
	}
</style>
