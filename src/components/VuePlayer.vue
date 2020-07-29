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
$light-color: #e0e0e0;
$dark-color: #181818;

.player {
	border-radius: 20px;
	box-shadow: 0 2px 10px rgba(10, 10, 10, 0.2);

	.player-wrapper {
		-webkit-mask-image: -webkit-radial-gradient(white, black);
		mask-image: linear-gradient(white, black);

		background-color: rgba($light-color, 0.3);
		border-radius: 20px;
		position: relative;
		overflow: hidden;
	}

	.player-controls {
		display: flex;
		flex-direction: row;
		flex-wrap: nowrap;
		align-items: center;

		.player-tracker {
			display: flex;
			flex-direction: row;
			flex-wrap: nowrap;
			align-items: center;
			width: 100%;

			.player-progress {
				flex-grow: 2;
				margin: 0 9px;
			}

			.player-time-current,
			.player-time-total {
				width: 30px;
				font-size: 0.8em;
				font-variant: tabular-nums;
				font-variant-numeric: tabular-nums;
				-moz-font-feature-settings: 'tnum';
				-webkit-font-feature-settings: 'tnum';
				font-feature-settings: 'tnum';
			}
		}
	}

	.action {
		color: inherit;
		cursor: pointer;
	}

	.player-tracker {
		box-sizing: border-box;
	}

	.player-progress {
		width: 100%;
		height: 5px;
		cursor: pointer;
		position: relative;
		background-color: rgba($light-color, 1);
		border-radius: 5px;

		.player-progress-wrapper {
			overflow: hidden;
		}

		.player-buffer {
			height: 100%;
			position: absolute;
			top: 0;
			left: 0;
			border-radius: 5px;
			background-color: rgba(255, 255, 255, 0.2);
		}

		.player-seeker {
			height: 100%;
			position: absolute;
			border-radius: 5px;
			top: 0;
			left: 0;
			transition: width 0.1s ease-in;
		}

		&:hover .player-seeker-thumb {
			opacity: 1;
		}
	}

	.player-seeker-thumb {
		display: inline-block;
		width: 10px;
		height: 10px;
		border-radius: 100%;
		position: absolute;
		transition: opacity 0.2s;
		opacity: 0;
		top: -2.5px;
		margin-left: -2.5px;
		box-shadow: 0 0 5px rgba(0, 0, 0, 0.1);
	}
}

.audio-player {
	.player-controls {
		min-height: 20px;
		padding: 5px 15px 5px 5px;

		.action {
			width: 13px;
			height: 13px;
			padding: 5px;
			transition: transform 0.2s;

			&:hover {
				transform: scale(1.2);
			}

			&:active {
				transform: scale(0.9);
			}
		}

		.player-progress {
			.player-buffer {
				background-color: rgba(0, 0, 0, 0.1);
			}
		}
	}
}

.video-player {
	video {
		display: block;
		object-fit: cover;
		object-position: center;
	}

	.player-wrapper {
		box-sizing: border-box;
		width: 100%;
		margin: 0 auto;

		&:hover {
			.player-controls {
				background-color: rgba(0, 0, 0, 0.8) !important;

				.action {
					opacity: 1;
				}

				.player-tracker {
					bottom: 10px;
					opacity: 1;
				}
			}
		}
	}

	.player-controls {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		transition: box-shadow 0.5s, background-color 0.3s;
		background-color: rgba(0, 0, 0, 0.8);
		color: #efefef;

		.action-pause {
			opacity: 0;
			fill: #efefef;
			stroke: transparent;
		}

		.action-pause,
		.action-play,
		.action-replay {
			position: absolute !important;
			top: 50%;
			left: 50%;
			width: 45%;
			height: 45%;
			transition: transform 0.3s, opacity 0.3s;
			transform: translate(-50%, -50%) scale(0.5);
			transform-origin: center;

			svg {
				margin: 0 auto;
			}

			&:active {
				transform: ranslate(-50%, -50%) scale(0.5);
			}

			&:hover {
				transform: translate(-50%, -50%) scale(0.7);
			}
		}

		.action-fullscreen {
			width: 20px;
			height: 13px;
			margin-left: 10px;
			transition: transform 0.3s;

			&:hover {
				transform: scale(1.3);
			}
		}

		.player-progress {
			background-color: rgba(255, 255, 255, 0.2);
		}

		.player-buffer {
			background-color: rgba(255, 255, 255, 0.3);
		}

		.player-tracker {
			position: absolute;
			left: 0;
			right: 0;
			bottom: -10px;
			padding: 0 15px;
			opacity: 0;
			transition: opacity 0.5s, bottom 0.2s;
		}
	}

	&.status-playing {
		.player-controls {
			background-color: transparent;

			.action {
				opacity: 0;
			}
		}

		.player-wrapper:hover {
			.action {
				opacity: 1;
			}
		}
	}
}
</style>
