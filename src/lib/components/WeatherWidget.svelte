<script>
	let weather = $state(null);
	let status = $state('idle');

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
		if (!navigator.geolocation) {
			status = 'unsupported';
			return;
		}

		status = 'loading';
		navigator.geolocation.getCurrentPosition(
			async ({ coords }) => {
				try {
					const query = new URLSearchParams({
						latitude: String(coords.latitude),
						longitude: String(coords.longitude),
						current: 'temperature_2m,weather_code',
						daily: 'temperature_2m_max,temperature_2m_min',
						timezone: 'auto',
						temperature_unit: 'celsius'
					});
					const response = await fetch(`https://api.open-meteo.com/v1/forecast?${query}`);
					if (!response.ok) throw new Error('Weather request failed');
					const data = await response.json();
					const [description, icon] = conditions[data.current?.weather_code] ?? ['Weather', '🌤️'];

					weather = {
						temperature: Math.round(data.current.temperature_2m),
						description,
						icon,
						high: Math.round(data.daily.temperature_2m_max[0]),
						low: Math.round(data.daily.temperature_2m_min[0])
					};
					status = 'ready';
				} catch {
					status = 'error';
				}
			},
			() => {
				status = 'denied';
			},
			{ enableHighAccuracy: false, maximumAge: 600000, timeout: 10000 }
		);
	}

	const prompt = $derived(
		status === 'loading'
			? 'Finding local weather…'
			: status === 'denied'
				? 'Location unavailable'
				: status === 'unsupported'
					? 'Location is not supported'
					: status === 'error'
						? 'Weather could not load'
						: 'Tap to use your location'
	);
</script>

<button class="ios-widget weather-widget" onclick={loadWeather} aria-label="Load weather for your location">
	<span class="widget-kicker">{weather ? 'NEAR YOU' : 'LOCAL WEATHER'}</span>
	<strong>{weather ? `${weather.temperature}°` : '—°'}</strong>
	<span>{weather ? `${weather.icon} ${weather.description}` : prompt}</span>
	<small>{weather ? `H:${weather.high}°  L:${weather.low}° · Open-Meteo` : 'Local forecast'}</small>
</button>
