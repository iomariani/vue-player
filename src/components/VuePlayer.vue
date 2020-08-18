<template>
	<div
		class="player"
		:class="{
			'audio-player': audio,
			'video-player': video,
			[`status-${status}`]: true,
			'fullscreen-active': fullscreenActive,
			theater
		}"
	>
		<transition name="fade">
			<div
				v-if="theater && (status === 'playing' || fullscreenActive)"
				class="player-overlay"
				:class="{ blurred: overlayBlur }"
				:style="`background-color: ${overlayColor}`"
			></div>
		</transition>

		<div class="player-wrapper" ref="wrapper">
			<audio
				v-if="audio"
				preload="auto"
				:autoplay="autoplay"
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
				v-if="video"
				:width="videoWidth"
				:height="videoHeight"
				preload="auto"
				:autoplay="autoplay"
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
				<replay-icon v-if="currentTime == duration && duration > 0" aria-label="replay" class="action action-replay" @click="replay" />
				<pause-icon v-else-if="status === 'playing'" aria-label="pause" class="action action-pause" viewBox="2 0 20 25" @click="pause" />
				<play-icon v-else aria-label="play" class="action action-play" viewBox="0 0 20 25" @click="play" />

				<template v-if="video">
					<backwards-icon
						v-if="status === 'playing'"
						aria-label="back ten seconds"
						class="action action-backwards"
						viewBox="0 0 20 25"
						@click="backwards"
					/>
					<forwards-icon
						v-if="status === 'playing'"
						aria-label="forward ten seconds"
						class="action action-forwards"
						viewBox="0 0 20 25"
						@click="forwards"
					/>
				</template>

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

					<template v-if="video">
						<maximize-icon
							v-if="!fullscreenActive && !autoFullscreen && fullscreenOption !== 'native'"
							aria-label="toggle fullscreen"
							class="action action-fullscreen"
							viewBox="0 0 20 25"
							@click="toggleFullscreen"
						/>
						<minimize-icon
							v-else-if="fullscreenActive && !autoFullscreen && fullscreenOption !== 'native'"
							aria-label="toggle fullscreen"
							class="action action-fullscreen"
							viewBox="0 0 20 25"
							@click="toggleFullscreen"
						/>
						<fullscreen-icon
							v-if="!autoFullscreen && fullscreenOption !== 'scale'"
							aria-label="toggle fullscreen"
							class="action action-fullscreen"
							viewBox="0 0 20 25"
							@click="requestFullscreen"
						/>
					</template>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import PlayIcon from '../icons/play.svg'
import PauseIcon from '../icons/pause.svg'
import ReplayIcon from '../icons/replay.svg'
import FullscreenIcon from '../icons/fullscreen.svg'
import MaximizeIcon from '../icons/maximize.svg'
import MinimizeIcon from '../icons/minimize.svg'
import BackwardsIcon from '../icons/backwards.svg'
import ForwardsIcon from '../icons/forwards.svg'

export default {
	name: 'vue-player',
	components: {
		PlayIcon,
		PauseIcon,
		ReplayIcon,
		FullscreenIcon,
		MaximizeIcon,
		MinimizeIcon,
		BackwardsIcon,
		ForwardsIcon
	},
	props: {
		audio: {
			type: Boolean,
			default: false
		},
		video: {
			type: Boolean,
			default: false
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
		autoplay: {
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
		exclusive: {
			type: Boolean,
			default: true
		},
		theater: {
			type: Boolean,
			default: false
		},
		overlayBlur: {
			type: Boolean,
			default: false
		},
		overlayColor: {
			type: String,
			default: '#000000e6'
		},
		fullscreen: {
			type: String,
			default: 'both'
		},
		autoFullscreen: {
			type: Boolean,
			default: false
		},
		viewport: {
			type: Function,
			default: () => window
		}
	},
	data() {
		return {
			status: 'loading',
			currentTime: 0,
			duration: 0,
			buffered: 0,
			progress: 0,
			fullscreenActive: false
		}
	},
	computed: {
		fullscreenOption() {
			const ua = navigator.userAgent
			const isMobile = /Android|webOS|iPhone|iPad|iPod/i.test(ua)

			if (isMobile) return 'native'
			else return this.fullscreen
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

				if (this.autoplay) this.play()

				return this.autoplay
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

			if (target.playing) this.status = 'playing'
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
				if (this.exclusive) {
					const players = document.getElementsByClassName('player status-playing')

					players.forEach(player => player.getElementsByClassName('media')[0].pause())
				}

				this.status = 'playing'
			}

			if (this.autoFullscreen) {
				if (this.fullscreenOption === 'native') this.requestFullscreen()
				else this.toggleFullscreen()
			}
		},
		replay() {
			this.$refs.player.pause()
			this.$refs.player.currentTime = 0
			this.$refs.player.play()
		},
		seek(e) {
			const el = e.target.classList.contains('player-progress') ? e.target : e.target.parentNode
			const rect = el.getBoundingClientRect()
			const seekPos = (e.clientX - rect.left) / rect.width

			this.$refs.player.currentTime = parseInt(this.duration * seekPos)
		},
		requestFullscreen() {
			const player = this.$refs.player

			if (player.requestFullscreen) {
				player.requestFullscreen()
			} else if (player.mozRequestFullScreen) {
				player.mozRequestFullScreen()
			} else if (player.webkitRequestFullScreen) {
				player.webkitRequestFullScreen()
			}
		},
		toggleFullscreen() {
			const wrapper = this.$refs.wrapper
			const player = this.$refs.player

			if (this.fullscreenActive) {
				wrapper.style.transform = ''
			} else {
				const $viewport = this.viewport.call()
				let viewport

				if ($viewport instanceof Window) {
					viewport = {
						width: window.innerWidth,
						height: window.innerHeight
					}
				} else {
					viewport = $viewport.getBoundingClientRect()
				}

				const playerRect = player.getBoundingClientRect()
				const scale = Math.min(viewport.width / player.offsetWidth, viewport.height / player.offsetHeight) * 0.85
				let centerX = viewport.width / 2 - (playerRect.width * scale) / 2 - playerRect.left
				let centerY = viewport.height / 2 - (playerRect.height * scale) / 2 - playerRect.top

				if ($viewport instanceof HTMLElement) {
					centerX += viewport.left
					centerY += viewport.top
				}

				wrapper.style.transform = `translate(${centerX}px, ${centerY}px) scale(${scale})`
			}

			this.fullscreenActive = !this.fullscreenActive
		},
		backwards($event, seconds = 10) {
			this.$refs.player.currentTime -= seconds
		},
		forwards($event, seconds = 10) {
			this.$refs.player.currentTime += seconds
		}
	},
	filters: {
		time: function(seconds) {
			if (!seconds) return '0:00'

			const dt = new Date(seconds * 1000)
			let time = dt.toISOString().substring(11, 19)

			if (dt.getUTCHours() == 0) {
				time = time.slice(3)

				if (dt.getUTCMinutes() < 10) time = time.slice(1)
			}

			return time
		}
	}
}
</script>

<style lang="scss">
@import '../scss/vue-player.scss';
</style>
