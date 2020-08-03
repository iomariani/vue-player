# vue-player

> Simple, lightweight, vue.js HTML5 audio/video player

[![GitHub version](https://badge.fury.io/gh/iomariani%2Fvue-player.svg)](https://badge.fury.io/gh/iomariani%2Fvue-player) [![bundlephobia](https://img.shields.io/bundlephobia/minzip/@iomariani/vue-player.svg?style=flat)](https://bundlephobia.com/result?p=v-emoji-picker@latest) [![vue2](https://img.shields.io/badge/vue-2.x-brightgreen.svg?style=flat)](https://vuejs.org/) [![CodeFactor](https://www.codefactor.io/repository/github/iomariani/vue-player/badge)](https://www.codefactor.io/repository/github/iomariani/vue-player)

![License](https://img.shields.io/github/license/iomariani/vue-player)

---

## Contents

- [Install](#install)
- [Usage](#usage)
	- [Globally](#registering-globally)
	- [Locally](#registering-locally)
	- [CSS](#css)
	- [HTML](#html)
- [Props](#props)
	- [Audio Props](#audio-props)
	- [Video Props](#video-props)
- [Demo](#demo)
- [License](#license)
- [Credits](#credits)

---

## Install

```bash
npm install @iomariani/vue-player
```

## Usage

### Registering Globally

If you want the component to be available globally:

```js
import Vue from 'vue'
import VuePlayer from '@iomariani/vue-player'

Vue.component('vue-player', VuePlayer)
```

### Registering Locally

If you want the component to be available locally:

```js
import VuePlayer from '@iomariani/vue-player'

new Vue({
	components: {
		VuePlayer
	}
})
```

### CSS

The component css is available seperately. You can just import it by:

```js
import '@iomariani/vue-player/dist/vue-player.css'
```

### HTML

![audio](https://i.imgur.com/pZzdqKZ.png)

```html
<vue-player audio />
```

![video](https://i.imgur.com/cqOGc7U.png)

```html
<vue-player video />
```

## Props

- `exclusive`: Boolean, only allow one player playing at a time, default `true`
- `autoplay`: Boolean, component autoplay, default `false`
- `loop`: Boolean, set player loop, default `false`
- `color`: String, color to use at the active trackbar, default `#2f96fd`
- `seekerColor`: String, color to use on the trackbar seeker when hovering, default `#2f96fd`
- `theater`: Boolean, wrap the player with an overlay div, default `false`
- `overlayBlur`: Boolean, add a blur filter effect to the overlay, default `false`
- `overlayColor`: String, color to use on the overlay div, default `rgba(0, 0, 0, 0.9)`

### Audio Props

- `audio` - Boolean, set player type as audio
- `sources`: Object, key value with type: source
	- **Example**: `{ "audio/mp3": "//localhost/audio.mp3" }`

### Video Props

- `video`: Boolean, set player type as video
- `videoWidth`: String, set video width, default `100%`
- `videoHeight`: String, set video height, default `auto`
- `poster`: String, url of the poster image to use
- `fullscreen`: String, type of fullscreen to use, default `custom` - `custom`: Scale and center the player to the viewport. - `native`: Use browser native `requestFullscreen` method.
- `autoFullscreen`: Boolean, active fullscreen mode on play
- `sources`: Object, key value with type: source
	- **Example**: `{ "video/mp4": "//localhost/video.mp4" }`

## Demo

Available at [codesandbox.io](https://s5mvo.csb.app/) ([sandbox](https://codesandbox.io/s/vue-player-s5mvo))

## License

[MIT](https://github.com/iomariani/vue-player/blob/master/LICENSE.md)

## Credits

- Icons by [feathericons.com](https://feathericons.com)