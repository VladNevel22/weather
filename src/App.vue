<template>
	<div class="weather" :class="weatherClass">
		<div class="container">
			<div class="card weather-form">
				<input
					type="text"
					class="weather-form-input"
					v-model="searchQuery"
					placeholder="Enter city"
					@keyup.enter="weatersSearch"
				/>
				<button class="weather-form-btn" @click="weatersSearch">search</button>
			</div>

			<div class="card weather-load" v-if="loading">Loading...</div>
			<div class="card weather-error" v-if="error">Error loading data</div>

			<div class="weather-info" v-show="!error && location && description">
				<div class="weather-info-text">
					<p class="card">{{ location }}</p>
					<p class="card">{{ temperatur }} С°</p>
					<p class="card">{{ description }}</p>
				</div>
			</div>
		</div>

		<div class="weather-bg">
			<video
				v-if="weatherClass === 'sunny'"
				class="weather-bg__video sunny"
				src="./assets/sunny.mp4"
				autoplay
				muted
				loop
				playsinline
			></video>

			<video
				v-if="weatherClass === 'rainy'"
				class="weather-bg__video rainy"
				src="./assets/rainy.mp4"
				autoplay
				muted
				loop
				playsinline
			></video>

			<video
				v-if="weatherClass === 'murky'"
				class="weather-bg__video murky"
				src="./assets/murky.mp4"
				autoplay
				muted
				loop
				playsinline
			></video>

			<video
				v-if="weatherClass === 'eather'"
				class="weather-bg__video eather"
				src="./assets/default.mp4"
				autoplay
				muted
				loop
				playsinline
			></video>
		</div>

		<!--
			<img
				src="./assets/earth.jpg"
				class="weather-bg__img eather"
				alt="earth"
			/>
			<img src="./assets/rainy.jpg" class="weather-bg__img rainy" alt="rainy" />
			<img
				src="./assets/пасмурно.avif"
				class="weather-bg__img murky"
				alt="murky"
			/>
			<img
				src="./assets/sunny.avif"
				class="weather-bg__img sunny"
				alt="sunny"
			/>
			-->
	</div>
</template>

<script>
export default {
	data() {
		return {
			location: '',
			temperatur: null,
			description: '',
			loading: false,
			error: false,
			searchQuery: '',
		}
	},
	computed: {
		weatherClass() {
			const desc = this.description.toLowerCase()
			if (desc.includes('sun')) return 'sunny'
			if (desc.includes('rain')) return 'rainy'
			if (
				desc.includes('cloud') ||
				desc.includes('overcast') ||
				desc.includes('mist')
			)
				return 'murky'
			return 'eather'
		},
	},
	methods: {
		weatersSearch() {
			if (!this.searchQuery.trim()) return

			this.loading = true
			this.error = false

			fetch(
				`https://api.weatherapi.com/v1/current.json?key=5ea28504787244cc99e200806252504&q=${this.searchQuery}`
			)
				.then(response => {
					if (!response.ok) throw new Error('Network response was not ok')
					return response.json()
				})
				.then(data => {
					this.location = data.location.name
					this.temperatur = data.current.temp_c
					this.description = data.current.condition.text
					this.resetSearchQuery()
					this.loading = false
				})
				.catch(error => {
					console.error(error)
					this.error = true
					this.loading = false
				})
		},
		resetSearchQuery() {
			this.searchQuery = ''
		},
	},
}
</script>
