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
