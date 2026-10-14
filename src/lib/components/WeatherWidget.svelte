<script>
	import { onMount } from 'svelte';

	let weather = $state(null);
	let status = $state('idle');
	const JAKARTA = { latitude: -6.2088, longitude: 106.8456 };

	const conditions = {
		0: ['Clear', '☀️'],
		1: ['Mostly clear', '🌤️'],
		2: ['Partly cloudy', '⛅'],
		3: ['Cloudy', '☁️'],
		45: ['Fog', '🌫️'],
		48: ['Fog', '🌫️'],
		51: ['Drizzle', '🌦️'],
		53: ['Drizzle', '🌦️'],
		55: ['Drizzle', '🌧️'],
		56: ['Freezing drizzle', '🌧️'],
		57: ['Freezing drizzle', '🌧️'],
		61: ['Rain', '🌧️'],
		63: ['Rain', '🌧️'],
		65: ['Heavy rain', '🌧️'],
		66: ['Freezing rain', '🌧️'],
		67: ['Freezing rain', '🌧️'],
		71: ['Snow', '🌨️'],
		73: ['Snow', '🌨️'],
		75: ['Heavy snow', '❄️'],
		77: ['Snow grains', '❄️'],
		80: ['Rain showers', '🌦️'],
		81: ['Rain showers', '🌧️'],
		82: ['Heavy showers', '🌧️'],
		85: ['Snow showers', '🌨️'],
		86: ['Snow showers', '❄️'],
		95: ['Thunderstorm', '⛈️'],
		96: ['Thunderstorm', '⛈️'],
		99: ['Thunderstorm', '⛈️']
	};

	function loadWeather() {
		if (status === 'loading') return;
		status = 'loading';
		const query = new URLSearchParams({
			latitude: String(JAKARTA.latitude),
			longitude: String(JAKARTA.longitude),
			current: 'temperature_2m,weather_code',
			daily: 'temperature_2m_max,temperature_2m_min',
			timezone: 'Asia/Jakarta',
			temperature_unit: 'celsius'
		});
		fetch(`https://api.open-meteo.com/v1/forecast?${query}`)
			.then((response) => {
				if (!response.ok) throw new Error('Weather request failed');
				return response.json();
			})
			.then((data) => {
				const [description, icon] = conditions[data.current?.weather_code] ?? ['Weather', '🌤️'];
				weather = {
					temperature: Math.round(data.current.temperature_2m),
					description,
					icon,
					high: Math.round(data.daily.temperature_2m_max[0]),
					low: Math.round(data.daily.temperature_2m_min[0])
				};
				status = 'ready';
			})
			.catch(() => (status = 'error'));
	}

	onMount(loadWeather);

	const prompt = $derived(
		status === 'loading'
			? 'Loading Jakarta weather…'
			: status === 'error'
				? 'Weather could not load'
				: 'Loading Jakarta weather…'
	);
</script>

<button class="ios-widget weather-widget" onclick={loadWeather} aria-label="Refresh Jakarta weather">
	<span class="widget-kicker">JAKARTA</span>
	<strong>{weather ? `${weather.temperature}°` : '—°'}</strong>
	<span>{weather ? `${weather.icon} ${weather.description}` : prompt}</span>
	<small>{weather ? `H:${weather.high}°  L:${weather.low}°` : 'Indonesia'}</small>
</button>
