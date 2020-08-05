# vue-player

Simple, lightweight, vue.js HTML5 audio/video player

[![GitHub version](https://badge.fury.io/gh/iomariani%2Fvue-player.svg)](https://badge.fury.io/gh/iomariani%2Fvue-player) [![bundlephobia](https://badgen.net/bundlephobia/minzip/@iomariani/vue-player)](https://bundlephobia.com/result?p=@iomariani/vue-player) [![vue2](https://img.shields.io/badge/vue-2.x-brightgreen.svg?style=flat)](https://vuejs.org/) [![CodeFactor](https://www.codefactor.io/repository/github/iomariani/vue-player/badge)](https://www.codefactor.io/repository/github/iomariani/vue-player) ![License](https://img.shields.io/github/license/iomariani/vue-player)

---

## Table of Contents

- [Demo](#demo)
- [Install](#install)
- [Usage](#usage)
	- [Global](#rglobal)
	- [Local](#local)
	- [CSS](#css)
	- [HTML](#html)
- [Props](#props)
	- [Sources](#sources)
- [Styling](#styling)
	- [Available Variables](#available-variables)
- [Todo](#todo)
- [Credits](#credits)
- [Author](#author)
- [License](#license)

---

## Demo

Available at [codesandbox.io](https://s5mvo.csb.app/) ([sandbox](https://codesandbox.io/s/vue-player-s5mvo))

---

## Install

```bash
npm install @iomariani/vue-player
```

## Usage

### Global

If you want the component to be available globally:

```js
import Vue from 'vue'
import VuePlayer from '@iomariani/vue-player'

Vue.component('vue-player', VuePlayer)
```

### Local

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

Param | Type | Description | Default
--- |:---:| --- |:---:
`exclusive`|`Boolean`|Allow only one player playing at a time|`true`
`autoplay`|`Boolean`|Audio/video autoplay property|`false`
`loop`|`Boolean`|Audio/video loop property|`false`
`color`|`String`|Color to use at the active trackbar|`#2f96fd`
`theater`|`Boolean`|Wrap the player with an overlay div|`false`
`overlayBlur`|`Boolean`|Add a blur filter effect to the overlay|`false`
`overlayColor`|`String`|Color to use on the overlay div|`#000000e6`
**Audio Props**|
`audio`|`Boolean`|Set player type as audio|`false`
`sources`|`Object`|[Declaration example below](#sources)
**Video Props**|
`video`|`Boolean`|Set player type as video|`false`
`videoWidth`|`String`|Video width|`100%`
`videoHeight`|`String`|Video height|`auto`
`poster`|`String`|URL of the poster image
`fullscreen`|`String`|Type of fullscreen to use. See types below|`both`
`autoFullscreen`|`Boolean`|Active fullscreen mode on play|`false`
`sources`|`Object`|[Declaration example below](#sources)
**Fullscreen Types**|
`native`||Browser native `requestFullscreen` method
`scale`||Scale the player to the viewport
`both`||Enable both `native` and `scale` methods

### Sources

Sources must be declared as an object as `type`:`source`. Example:
```js
const audioSources = {
	"audio/mp3": "//localhost/music.mp3",
	...
};

const videoSources = {
	"video/mp4": "//localhost/video.mp4",
	"video/webm": "//localhost/video.webm",
	...
};
```

## Styling

If you want to style the player you can do so by importing the scss file:

```html
<style lang="scss">
@import '@iomariani/vue-player/src/scss/vue-player.scss';
</style>
```

### Available Variables

$var | default
---|---:|
`$player-background`|`#f0f0f0`
`$player-border-radius`|`20px`
`$player-buffer-background` | `#ffffff33`
`$player-fullscreen-z-index` | `10000`

## Todo

- [ ] Loading/buffering icon
- [ ] Volume slider
- [ ] Trackbar seeker on drag
- [x] Skip forward 10s
- [x] Skip backwards 10s
- [x] Custom Fullscreen
- [x] Theater Mode
- [x] Exclusive Mode

## Credits

- Icons by [feathericons.com](https://feathericons.com)

## Author

- [Marcos Mariani](https://github.com/iomariani)

## License

[MIT](https://github.com/iomariani/vue-player/blob/master/LICENSE.md)