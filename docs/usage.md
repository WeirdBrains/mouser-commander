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

## Context menu

Right-click in the view (or `.` while commander has the keyboard, Mouser 1.4).

Commander's menus (keys are the defaults):

| Where | Entries |
| --- | --- |
| a file | open (Enter), copy, copy to…, move to…, rename…, delete…, copy path, mark (Space), pipe (`\|`), back (`u`) |
| an image, video or audio file | the same, with play (Enter) and play / view fullscreen |
| a folder | open (Enter), sync the shell here, the file entries, paste, back (`u`) |
| the empty area | back, sync (`s`), new folder…, paste, sort by name / modified / size, reverse, switch view (`v`), larger / smaller tiles (`z` / `x`), dotfiles (`h`), search (`/`), mark all (`*`), first / last, options (`o`), manual (`m`) |
| image view, fullscreen or not | zoom in / out (`=` / `-`), fit (`0`), 100% (`1`), pan (arrows; ← / → step images when not zoomed), fullscreen / exit fullscreen (`f`), copy path, close (`q`) |
| player, fullscreen or not | play / pause (`k`), back / forward 10 s (`j` / `l`), volume (`-` / `=`), fullscreen / exit fullscreen (`f`), copy path, close (`q`) |
| text, Markdown, PDF or document view, the manual | scroll (↑ / ↓), page up / down, previous / next file (← / →), copy text, copy path, close (`q`) |

File changes never happen straight away: copy to, move to, rename and new folder
first ask for the destination or name in commander's prompt line (Enter goes on,
Esc cancels), then every change (delete and paste too) waits for your yes on
Mouser's approval card, which lists what will change. Nothing is ever
overwritten: a target that exists stops the change. Delete is permanent (folders
with everything in them); the card says so. `copy` remembers the items for
`paste` in another folder; `copy path` and `copy text` put text on the
clipboard. File changes are local only (greyed out in a remote folder), and the
commander plugin declares `files.write` for them. The text view has no zoom of
its own (use Mouser's font size, ctrl+= / ctrl+-).

## Panel size

Mouser 1.4 lets you size the mode panel (`F` fullscreen, `]` / `[` taller /
shorter while the mode has the keyboard, or drag its bottom edge); this plugin
lays its panel out to that size (`dock` in `view.resized`). Older Mouser
versions keep the plugin's own height.
