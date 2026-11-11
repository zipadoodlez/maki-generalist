![maki-generalist](assets/maki-generalist-swissarmyknife.png)

# maki-generalist

A maki plugin that makes maki a generalist.

Maki is a coding agent. A generalist does more than code: it answers questions,
plans work, writes and organizes documents, and writes code when the work is
code. This plugin replaces the system prompt with one that says so.

## What it does

`maki.api.set_prompt` replaces the whole system prompt. The `{{...}}` slots in
the text (`{{objectivity}}`, `{{tool_usage}}`, `{{conventions}}`, and the rest)
are filled by maki, so the prompt keeps the wiring and changes only the stance.

Editing that text is the whole configuration. There is no config file and no
options.

## Install

Add it to your global `~/.config/maki/init.lua`:

```lua
maki.pack.add({ "https://github.com/zipadoodlez/maki-generalist" })
```

Update it with `/packupdate`. It asks for no permissions, because replacing a
prompt is not gated.

## The one that matters

The first line is the whole point:

> You are Maki, a general-purpose assistant with full access to the user's
> machine.

Everything below it is the discipline that keeps a generalist useful: lead with
the point, act only when asked, do not invent URLs or paths, and do not do more
than was asked.
