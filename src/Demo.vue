<template>
	<div id="app">
		<h1>Vue.js Audio/Video Player</h1>

		<div class="container">
			<vue-player audio theater :sources="audioSources" />
			<vue-player video theater :sources="videoSources" />
		</div>
	</div>
</template>

<script>
import VuePlayer from './components/VuePlayer.vue'

export default {
	name: 'Demo',
	components: {
		VuePlayer
	},
	data() {
		return {
			audioSources: {
				'audio/mp3': 'https://www.bensound.com/bensound-music/bensound-littleplanet.mp3'
			},
			videoSources: {
				'video/mp4': 'https://mdn.github.io/learning-area/javascript/apis/video-audio/finished/video/sintel-short.mp4',
				'video/webm': 'https://mdn.github.io/learning-area/javascript/apis/video-audio/finished/video/sintel-short.webm'
			}
		}
	}
}
</script>

<style lang="scss">
.container {
	width: 500px;
	margin: 0 auto;
}

.player {
	margin: 0 auto 2em;
}

#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}
</style>
