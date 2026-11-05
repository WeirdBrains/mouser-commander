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

## How it works

It is a separate program Mouser starts once and keeps running, speaking the
[mode protocol](https://github.com/WeirdBrains/mouser-mode-api/blob/main/docs/protocol.md)
over stdio, with images through shared memory. Mouser's own end-to-end tests drive it
through Mouser's adapter, so those tests live in [Mouser](https://github.com/WeirdBrains/mouser).
