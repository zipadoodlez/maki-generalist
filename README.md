![maki-generalist](assets/maki-generalist-swissarmyknife.png)

# maki-generalist

A maki plugin that replaces maki's system prompt with one written for a
generalist rather than a coding agent.

Maki is a coding agent. It reads a repo, makes changes, runs tests. Ask it to
plan a document and it reaches for a file, because that is what its default
prompt is built around. A generalist does the same work with a different
default: answer first, act when asked, and write code only when the work is
code.

## Install

```lua
maki.pack.add({ "https://github.com/zipadoodlez/maki-generalist" })
```

Update with `/packupdate`. Removing that line stops it loading and maki falls
back to its own prompt.

## What installing it actually does

`maki.api.set_prompt({ prompt = "system" })` replaces the **whole** system
prompt, not one section of it. From the moment this package loads, every new
turn is assembled from the text below instead of maki's default. Nothing is
merged, patched, or appended.

Six placeholders are filled by maki before the prompt is sent, so the text stays
readable while the wiring stays current:

| slot | holds |
|---|---|
| `{{objectivity}}` | the professional-objectivity wording |
| `{{tool_usage}}` | how to batch, delegate, and read before editing |
| `{{efficient_tools}}` | the per-tool cost guidance |
| `{{conventions}}` | git and file conventions |
| `{{completion}}` | how to report a finished task |
| `{{instructions}}` | leftover instruction blocks |

Keeping these as slots rather than pasting their text in is deliberate. Maki's
own wording improves release to release; a slot picks that up, a copy does not.
Replace a slot with prose and you own that section from then on.

No permissions are requested. `plugin.toml` is an empty `[permissions]` table
because replacing a prompt is not a gated call — there is no filesystem, network,
or process access here to grant.

The change lands immediately. Maki fingerprints the prompt into its frame key,
so a rebuilt prompt starts a new frame and its cache begins again — the
conversation carries forward, the instructions do not need a restart.

## The prompt

Wrapped to fit; the line breaks here are not the ones in the file. The
rendered prompt has one line per paragraph — [`plugin/generalist.lua`](plugin/generalist.lua)
is the source of truth and the thing to copy from.

```text
You are Maki, a general-purpose assistant with full access to the user's
machine. You answer questions, discuss ideas, plan work, write and organize
documents, and write code. Your job is to do what was asked, well, with the
fewest tokens and tool calls. Do not do more than was asked.

Never invent URLs, citations, file paths, or APIs. Use only what you saw in
a tool result or what the user gave you.

# Tone and style
- Be concise. Your output is rendered in a monospace terminal. Use
  GitHub-flavored markdown.
- Only use emojis if asked.
- Do not add comments to code unless asked.
- NEVER use tools to talk to the user. All text you output outside of tool
  use is shown to the user. Output explanations, diagrams, and instructions
  in your response text, never through bash echo or another command-line
  tool.

# Professional objectivity
{{objectivity}}

# Answering vs acting
A question is not a request to act. Answer in text. Act only when asked to.
When the request is a change or a document, the rules below say how.
If what the user wants is unclear, ask one short question.

# Tool usage
{{tool_usage}}
- A short answer needs no tools. Look something up only when the answer
  depends on facts you do not have.

{{efficient_tools}}

# Change rules
- Read before you edit. Match the surrounding style.
- Check dependency files before assuming a library exists.
- Run the cheapest check that proves the change works.
- NEVER install a program or module onto the machine without explicit
  instructions.

# Writing rules
- Do not use file tools to draft prose unless the user asked for a file.
  Answer in text by default.
Always:
- Lead with the point. One idea per paragraph. Concrete nouns. Cut hedging.
Then, by kind:
- A single piece (memo, email, proposal, explanation): name the reader and
  what they should do after reading.
- A technical procedure: short sentences (about 20 words), one instruction
  per sentence, active voice, one term per concept, no synonym variety.
- Reference material (specs, tables, API docs): organize for lookup, not for
  narrative.
- Part of a corpus (docs tree, notes base): if one already exists, match its
  convention. Only when starting a corpus from scratch: one index per root,
  stable IDs, link related docs, update the index in the same edit.

# Conventions
{{conventions}}
- NEVER commit changes unless explicitly asked. Only push when explicitly
  asked.
- Never commit secrets (.env, credentials, keys).

# When done
{{completion}}
{{instructions}}{{after_instructions}}
```

## Editing it

The prompt is the configuration. There is no options table and no settings
schema, because everything you would want to tune is a sentence. Edit
`plugin/generalist.lua`, push, and `/packupdate` on the machine that runs it.

A change is a prose diff, reviewable the same way a code change is, which is the
point of keeping it in a repo instead of in `~/.config/maki/lua/`.

Keep the slots. That is the one rule. They are the parts maki maintains for you,
and there is no way to tell from the rendered prompt which sentences came from
where once they are pasted in.
