# Plainpaper Skills

Agent Skills for the platforms marketers actually run campaigns in.

Your AI agent already knows what a welcome flow *is*. What it doesn't know is that Brevo's automation
builder ignores drag-and-drop even though its own UI tells you to drag, that the canvas takes three
seconds to repaint so a screenshot taken too fast makes you add every step twice, or that deleting a
step has no confirmation dialog. That kind of knowledge isn't in the model and isn't in the API docs —
it only exists once somebody has sat there and worked it out.

This repository is where we write it down.

## What an Agent Skill is

A skill is a folder with a `SKILL.md` in it: some YAML metadata and a set of instructions. Agents load
them **progressively** — at startup an agent reads only each skill's name and description, a few dozen
tokens, just enough to know when it might be relevant. The full instructions load only when a task
actually matches, and bulky reference material stays in `references/` until it's needed.

That's the whole idea, and it's why skills scale: you can have fifty installed and pay almost nothing
for the forty-nine that aren't relevant right now.

```
brevo-automation-builder/
├── SKILL.md                      metadata + the procedure
└── references/
    ├── brevo-ui-map.md           the builder, mapped
    └── briefing-checklist.md     what a briefing has to contain
```

[Agent Skills](https://agentskills.io) is an open standard, originally developed by Anthropic and now
read by Claude Code, Codex, Cursor, GitHub Copilot, Gemini CLI, VS Code, Goose, OpenHands, Amp and
dozens of others. Nothing here is Claude-only.

## Install

**Claude Code:**

```
/plugin marketplace add plainpaperio/skills
/plugin install brevo@plainpaper
```

**Claude desktop app:** open the **Cowork** tab, then **Customize → Plugins**, add this repository as
a marketplace, and install from there.

**Any other Agent-Skills client:** every skill folder under `plugins/*/skills/` is a valid skill on its
own. Copy the one you want into whatever skills directory your client reads — `~/.codex/skills/`,
`~/.claude/skills/`, `~/.config/gemini/skills/` and so on. No plugin machinery required.

To pull in changes later: `/plugin marketplace update plainpaper`. You only receive an update when a
plugin's `version` changes.

## What's here

| Plugin | Skill | What it does |
|---|---|---|
| `brevo` | `brevo-automation-builder` | Builds a marketing automation in Brevo from a written briefing, by driving the automation builder in your browser. Confirms which account it's in first, saves as an **inactive draft**, and hands back a direct link. |

More platforms are coming. One plugin per platform, named after the platform.

### Requirements

Each skill declares its own in the `compatibility` field of its `SKILL.md`. For
`brevo-automation-builder` specifically: it needs a browser-automation tool that drives **your own,
already-signed-in browser** — it was built against the Claude in Chrome extension, with permission for
`app.brevo.com`.

A headless or fresh-profile browser will not work, and that's deliberate: the skill never handles your
Brevo credentials, so it depends on a session you have already signed into yourself. Brevo exposes no
automation endpoint in its API or its MCP server, so the browser is the only route to automations at
all.

Optional but recommended: the Brevo MCP connector, so lists, segments, templates and senders get
looked up by name and ID instead of scrolled through in dropdowns.

## Safety

Every skill here is written to stop before anything irreversible:

- **Nothing goes live.** The Brevo skill will not click "Activate automation" — that's your click,
  after you've reviewed the draft.
- **Nothing gets sent.** It will not use Brevo's "Test" button, which sends real email, unless you ask
  for it.
- **No credentials, ever.** If you're not signed in, the skill stops and asks you to sign in yourself.
  No skill in this repository may type a password or accept one pasted into chat.
- **Existing work is left alone** unless you point at it explicitly.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) — it covers the layout, how to write a skill description that
actually triggers, and the conventions that keep skills portable across clients.

The most valuable contribution isn't code. It's the thing you learned at 11pm about why a platform's
UI lies to you.

## Who makes this

These skills come out of building [Plainpaper](https://plainpaper.io) — a shared, structured workspace
for marketing run through AI. Your agent does the work and writes it to a board of typed cards
(research, audiences, plans, emails, creative, results); you read, steer and approve. The point is that
an agent can open a fresh session and reconstruct an entire campaign from the board, instead of relying
on a chat transcript that scrolled away.

Plainpaper never sends anything itself. Your agent reaches each platform through that platform's own
integration — which is exactly why these skills exist, and why they live in their own repository under
an open licence rather than locked inside a product.

**You do not need a Plainpaper account to use anything here.** Every skill works standalone.

If you do use Plainpaper, the connection is that a board tells your agent which skills matter for the
tools it has enabled — so an agent opening a campaign that contains an automation is told the Brevo
skill exists before it starts guessing. You can [sign up at app.plainpaper.io](https://app.plainpaper.io/signup).

## Licence

[MIT](LICENSE). Use them, fork them, ship them in your own product.
