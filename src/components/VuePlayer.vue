<template>
	<div class="player" :class="{ [`${type}-player`]: true, [`status-${status}`]: true }">
		<div class="player-wrapper">
			<audio
				v-if="type === 'audio'"
				preload="auto"
				:autoplay="autoPlay"
				:loop="loop"
				@progress="progressListener"
				@loadeddata="loaded"
				@timeupdate="timeUpdate"
				@play="playPause"
				@pause="playPause"
				@error="error"
				class="media"
				ref="player"
			>
				<source v-for="(src, type) of sources" :src="src" :type="type" :key="type" />
				Your browser does not support HTML5 audio.
			</audio>

			<video
				v-if="type === 'video'"
				:width="videoWidth"
				:height="videoHeight"
				preload="auto"
				:autoplay="autoPlay"
				:loop="loop"
				:poster="poster"
				@progress="progressListener"
				@loadeddata="loaded"
				@timeupdate="timeUpdate"
				@play="playPause"
				@pause="playPause"
				@error="error"
				class="media"
				ref="player"
			>
				<source v-for="(src, type) of sources" :src="src" :type="type" :key="type" />
				Your browser does not support HTML5 video.
			</video>

			<div class="player-controls">
				<skip-back-icon
					v-if="currentTime == duration && duration > 0"
					aria-label="replay"
					class="action action-replay"
					viewBox="0 0 20 25"
					@click="replay"
				/>
				<pause-icon v-else-if="status === 'playing'" aria-label="pause" class="action action-pause" viewBox="2 0 20 25" @click="pause" />
				<play-icon v-else aria-label="play" class="action action-play" viewBox="0 0 20 25" @click="play" />

				<div class="player-tracker">
					<span class="player-time-current" aria-label="current time">{{ currentTime | time }}</span>
					<div class="player-progress" @click="seek">
						<div class="player-progress-wrapper">
							<div class="player-buffer" :style="`width: ${buffered}%`"></div>
							<div class="player-seeker" :style="`background-color: ${color}; width: ${progress}%`"></div>
						</div>
						<div class="player-seeker-thumb" :style="`background-color: ${color}; left: ${progress}%`"></div>
					</div>
					<span class="player-time-total" aria-label="duration">{{ duration | time }}</span>

					<fullscreen-icon
						v-if="video === true"
						aria-label="toggle fullscreen"
						class="action action-fullscreen"
						viewBox="0 0 20 25"
						@click="fullscreen"
					/>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import PlayIcon from '../assets/icons/play.svg'
import PauseIcon from '../assets/icons/pause.svg'
import SkipBackIcon from '../assets/icons/skip-back.svg'
import FullscreenIcon from '../assets/icons/maximize.svg'

export default {
	name: 'vue-player',
	components: {
		PlayIcon,
		PauseIcon,
		SkipBackIcon,
		FullscreenIcon
	},
	props: {
		type: {
			type: String,
			default: 'audio'
		},
		sources: {
			type: Object
		},
		videoWidth: {
			type: String,
			default: '100%'
		},
		videoHeight: {
			type: String,
			default: 'auto'
		},
		autoPlay: {
			type: Boolean,
			default: false
		},
		loop: {
			type: Boolean,
			default: false
		},
		poster: {
			type: String
		},
		color: {
			type: String,
			default: '#2f96fd'
		},
		seekerColor: {
			type: String,
			default: '#2f96fd'
		}
	},
	data() {
		return {
			status: 'loading',
			currentTime: 0,
			duration: 0,
			buffered: 0,
			progress: 0
		}
	},
	methods: {
		progressListener(e) {
			const player = e.target
			const duration = player.duration

			if (duration > 0) {
				for (let i = 0; i < player.buffered.length; i++) {
					if (player.buffered.start(player.buffered.length - 1 - i) < player.currentTime) {
						this.buffered = (player.buffered.end(player.buffered.length - 1 - i) / duration) * 100
						break
					}
				}
			}
		},
		loaded(e) {
			const target = e.target

			if (target.readyState >= 2) {
				this.status = 'loaded'
				this.duration = parseInt(target.duration)

				if (this.autoPlay) this.play()

				return this.autoPlay
			} else {
				this.error(e)
			}
		},
		error(e) {
			switch (e.target.error.code) {
				case e.target.error.MEDIA_ERR_ABORTED:
					throw new Error('You aborted the video playback.')
				case e.target.error.MEDIA_ERR_NETWORK:
					throw new Error('A network error caused the audio download to fail.')
				case e.target.error.MEDIA_ERR_DECODE:
					throw new Error(
						'The audio playback was aborted due to a corruption problem or because the video used features your browser did not support.'
					)
				case e.target.error.MEDIA_ERR_SRC_NOT_SUPPORTED:
					throw new Error('The video audio not be loaded, either because the server or network failed or because the format is not supported.')
				default:
					throw new Error('An unknown error occurred.')
			}
		},
		timeUpdate(e) {
			const target = e.target

			this.currentTime = parseInt(target.currentTime)
			this.progress = (this.currentTime / this.duration) * 100
		},
		play() {
			this.$refs.player.play()
		},
		pause() {
			this.$refs.player.pause()
		},
		playPause(e) {
			if (e.type === 'pause') {
				this.status = 'paused'
			} else if (e.type === 'play') {
				const players = document.getElementsByClassName('con-player status-playing')

				players.forEach(player => {
					let media = player.getElementsByClassName('media')[0]

					if (media.tagName === 'AUDIO' || media.tagName === 'VIDEO') media.pause()
				})

				this.status = 'playing'
			}
		},
		replay() {
			this.$refs.player.pause()
			this.$refs.player.currentTime = 0
			this.$refs.player.play()
		},
		seek(e) {
			if (e.target.tagName === 'SPAN') {
				return
			}

			const el = e.target.classList.contains('player-progress') ? e.target : e.target.parentNode
			const rect = el.getBoundingClientRect()
			const seekPos = (e.clientX - rect.left) / rect.width

			this.$refs.player.currentTime = parseInt(this.duration * seekPos)
		},
		fullscreen() {
			const player = this.$refs.player

			if (player.requestFullscreen) {
				player.requestFullscreen()
			} else if (player.mozRequestFullScreen) {
				player.mozRequestFullScreen()
			} else if (player.webkitRequestFullScreen) {
				player.webkitRequestFullScreen()
			}
		}
	},
	filters: {
		time: function(seconds) {
			if (!seconds) return '00:00'

			const dt = new Date(seconds * 1000)
			const time = dt.toISOString().substr(11, 8)

			if (dt.getUTCHours() == 0) return time.substring(3)
			else return time
		}
	}
}
</script>

<style lang="scss">
@import '../scss/vue-player.scss';
</style>
