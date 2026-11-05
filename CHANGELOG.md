# Changelog

## [Unreleased]

### Added

- Back works like `popd`: entering a folder or file pushes where you were (logical path, selection, scroll) and back restores it exactly; with an empty stack back goes to the parent of the logical path, never a symlink target's real parent. Remote folders too. See docs/usage.md.

## [0.1.1] - 2026-11-05

### Fixed

- The Linux release is built on Ubuntu 22.04 (glibc 2.35), so it loads on older distributions too; 0.1.0 needed glibc 2.43.

## [0.1.0] - 2026-11-05

### Added

- First release on its own, extracted from [Mouser](https://github.com/WeirdBrains/mouser) with its history. Mouser's commander mode as a plugin: the shell's folder above the prompt, with previews and the media player.
