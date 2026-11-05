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
