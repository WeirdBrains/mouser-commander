# Changelog

## [Unreleased]

## [0.1.3] - 2026-11-05

### Changed

- Keys follow Mouser's focus model: they are plain (`s` sync, `/` search, `v` view, `u` back, `h` dotfiles, `f` fullscreen, arrows, Enter ...) and only reach commander while it has the keyboard (Mouser's ctrl+alt+k toggle or a click), so no bash, zsh or fish binding is shadowed. A key commander passes on is no longer typed into the shell. docs/usage.md lists them.
- Sync (`s`) moves the shell to commander's folder through the shell integration only (never typed); the folder commander shows follows the shell only when the shell's folder changes, and browsing is listed locally or over the ssh side channel. The old typed `cd` line is gone.

### Added

- Audio output from Mouser's preferences: `audio.host` (auto/jack/alsa; auto tries JACK first, PipeWire's JACK included) and `audio.output_device` (falls back to the default with a notice). The player's status shows the host. JACK is the `jack` feature, on in the released Linux binaries. See docs/usage.md §Audio.

### Fixed

- Windows: audio hosts and devices are probed on one thread, so parallel probes no longer crash WASAPI.

## [0.1.2] - 2026-11-05

### Added

- Back works like `popd`: entering a folder or file pushes where you were (logical path, selection, scroll) and back restores it exactly; with an empty stack back goes to the parent of the logical path, never a symlink target's real parent. Remote folders too. See docs/usage.md.

## [0.1.1] - 2026-11-05

### Fixed

- The Linux release is built on Ubuntu 22.04 (glibc 2.35), so it loads on older distributions too; 0.1.0 needed glibc 2.43.

## [0.1.0] - 2026-11-05

### Added

- First release on its own, extracted from [Mouser](https://github.com/WeirdBrains/mouser) with its history. Mouser's commander mode as a plugin: the shell's folder above the prompt, with previews and the media player.
