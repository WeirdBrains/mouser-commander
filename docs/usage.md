# The commander mode

Mouser's commander mode as a plugin: the shell's folder above the prompt, with previews and the media player.

## Install

Mouser installs it on first start. To install or update it yourself:

```sh
mouser plugin install commander            # the version this Mouser pins
mouser plugin install gh:WeirdBrains/mouser-commander   # the latest release
```

Or in Mouser: `esc :pi commander`.

## Use

Open it from Mouser's mode line (`esc :` then its hotkey or name). Its keys are listed in
[plugin.toml](../plugin.toml) and shown by Mouser (`esc :help`). Permissions it asks for:
`files.read`, `shell.type`, `remote.read`.

### Keys

Commander takes no key from the shell. Without focus Mouser sends it nothing; one
key, Mouser's focus toggle **ctrl+alt+k** (free in bash, zsh and fish defaults), or a
click in the view gives it the keyboard. Then its keys are plain and nothing typed
reaches the shell; Esc, `q` with nothing open, or the toggle give the keyboard back.
All keys are remappable in Mouser's `modes.toml` (`[commander.keys]`, `[focus] key`).
On Windows Ctrl+Alt is also AltGr: remap the toggle if AltGr+K types a character on
your layout. Needs a Mouser with the focus model (after 1.3.1); older Mouser hosts
read these keys as alt+key.

| key | action |
| --- | --- |
| ↑ ↓ ← → | move |
| Enter | open (folder: browse it; file: view; media: player) |
| u, Backspace | back (like `popd`) |
| PgUp / PgDn, Home / End | page, first / last |
| v | switch view (list / grid / carousel) |
| / | search |
| s | sync: move the shell to commander's folder, silently through the shell integration (never typed) |
| space, *, \| | mark, mark all, pipe the marked paths into a command |
| z / x | larger / smaller tiles |
| h | dotfiles on / off |
| o / m / i | options / manual / header menu |
| q, Esc | close the view; with nothing open, back to the shell |
| k, j / l | player: play / pause, back / forward 10 s |
| - / = | player volume; image zoom out / in |
| 0 / 1 | image: fit / 100 % |
| f | fullscreen |


## Navigation

Back works like `popd`, inside commander (it never runs `pushd`/`popd`). Entering a
folder or opening a file pushes where you were onto a navigation stack: the path as
you saw it (a symlink stays a symlink), the selection and the scroll. Back pops it
and puts you exactly there again. With nothing on the stack (after a `cd` you typed,
a breadcrumb jump, or at startup) back goes to the parent of the path you see, not
of the resolved one, so leaving a symlinked folder never lands in its target's real
parent. Remote (SSH) folders work the same way.

## How it works

It is a separate program Mouser starts once and keeps running, speaking the
[mode protocol](https://github.com/WeirdBrains/mouser-mode-api/blob/main/docs/protocol.md)
over stdio, with images through shared memory. Mouser's own end-to-end tests drive it
through Mouser's adapter, so those tests live in [Mouser](https://github.com/WeirdBrains/mouser).

## Audio

The player's sound goes where Mouser's preferences say (`[audio]` in
`$MOUSER_HOME/preferences.toml`, Mouser's docs/preferences.md §Audio):

- `audio.host`: `auto` (JACK when a JACK server answers, PipeWire's JACK included,
  else ALSA), `jack` or `alsa`. Linux only; macOS uses CoreAudio, Windows WASAPI.
- `audio.output_device`: a device of that host; unset or `default` = its default.
  A device that is gone falls back to the default and the player says so.

The status beside play/pause shows the host playing (`playing · JACK`). JACK is the
`jack` cargo feature: building it needs the JACK headers and `jack.pc` (Debian/Ubuntu
`libjack-jackd2-dev`, Arch `pipewire-jack` or `jack2`); libjack is loaded at run
time, so a JACK build still plays through ALSA without it. The released Linux
binaries include it; `cargo install mouser-commander --features jack` does too.
