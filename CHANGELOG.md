# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

- Loading/buffering icon
- Volume slider
- Trackbar seeker on drag

## [0.5.3] - 2020-10-09

### Added

- Theater prop as `fullscreen` to enable only when fullscreen is active

## [0.5.2] - 2020-08-18

### Added

- Viewport prop for fullscreen `scale` option
- Force fullscreen to `native` if is on mobile

### Fixed

- Hide `scale` fullscreen option when `native` is set

## [0.4.7] - 2020-08-04

### Fixed

- Set default `.player-wrapper` z-index when overlay is visible

## [0.4.6] - 2020-08-04

### Fixed

- Set default `.player-overlay` z-index for `theater` mode

## [0.4.5] - 2020-08-04

### Changed

- `$player-fullscreen-z-index` default to `100000`

## [0.4.4] - 2020-08-04

### Fixed

- SCSS `!default` flag

## [0.4.3] - 2020-08-04

### Added

- SCSS Variables
- Fullscreen `z-index`

## [0.4.2] - 2020-08-04

### Changed

- Shift transform types for smoother transition

### Removed

- `seekerColor` prop

## [0.4.1] - 2020-08-04

### Fixed

- Fullscreen `custom` scale and offset calculations

## [0.4.0] - 2020-08-03

### Released

- Skip forward 10s
- Skip backwards 10s

### Added

- Allow `both` for `fullscreen` prop

## [0.3.0] - 2020-08-03

### Released

- Custom Fullscreen

### Changed

- Change time format to m:ss if minutes are under 10

### Added

- overlayColor Prop
- fullscreen Prop
- autoFullscreen Prop

### Removed

- Removed classes from SVG

## [0.2.1] - 2020-07-30

### Changed

- Make fullscreen overlay blur filter optional (prop _overlayBlur_)

## [0.2.0] - 2020-07-29

### Released

- Theater Mode
- Exclusive Mode

## [0.1.2] - 2020-07-29

### Added

- CHANGELOG.md file
- Prop audio for enabling HTML5 audio
- Prop video for enabling HTML5 video
- Demo.vue for testing purposes

### Removed

- Prop type

### Changed

- Moved component scss to vue-player.scss

### Fixed

- Export component from main

## [0.1.1] - 2020-07-29

### Added

- Prop type

### Fixed

- Export component from main

## [0.1.0] - 2020-07-29

### Added

- First release bundle

[unreleased]: https://github.com/iomariani/vue-player/compare/v0.5.3...HEAD
[0.5.3]: https://github.com/iomariani/vue-player/releases/tag/v0.5.3
[0.5.2]: https://github.com/iomariani/vue-player/releases/tag/v0.5.2
[0.4.7]: https://github.com/iomariani/vue-player/releases/tag/v0.4.7
[0.4.6]: https://github.com/iomariani/vue-player/releases/tag/v0.4.6
[0.4.5]: https://github.com/iomariani/vue-player/releases/tag/v0.4.5
[0.4.4]: https://github.com/iomariani/vue-player/releases/tag/v0.4.4
[0.4.3]: https://github.com/iomariani/vue-player/releases/tag/v0.4.3
[0.4.2]: https://github.com/iomariani/vue-player/releases/tag/v0.4.2
[0.4.1]: https://github.com/iomariani/vue-player/releases/tag/v0.4.1
[0.4.0]: https://github.com/iomariani/vue-player/releases/tag/v0.4.0
[0.3.0]: https://github.com/iomariani/vue-player/releases/tag/v0.3.0
[0.2.1]: https://github.com/iomariani/vue-player/releases/tag/v0.2.1
[0.2.0]: https://github.com/iomariani/vue-player/releases/tag/v0.2.0
[0.1.2]: https://github.com/iomariani/vue-player/releases/tag/v0.1.2
[0.1.1]: https://github.com/iomariani/vue-player/releases/tag/v0.1.1
[0.1.0]: https://github.com/iomariani/vue-player/releases/tag/v0.1.0
