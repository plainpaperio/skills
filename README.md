# Plainpaper skills

A Claude plugin marketplace with skills for the platforms marketers actually run campaigns in.

Plainpaper is the board your agent writes to. It is deliberately not the execution layer — it never
sends an email and never posts an ad. Your agent does that through each platform's own MCP server or
console. But "reach the platform" and "know how that platform works" are different problems, and the
second one is what lives here: the click-paths, the field names, the traps that only show up once you
are three steps into somebody's automation builder.

## Install

In Claude Code:

```
/plugin marketplace add thi3rrydereus/plainpaper-skills
/plugin install brevo@plainpaper
```

In the Claude desktop app: open the **Cowork** tab, then **Customize → Plugins**, add this repository
as a marketplace, and install the plugin from there.

Later, to pull in changes:

```
/plugin marketplace update plainpaper
```

You only receive an update when the plugin's `version` field changes.

## What is in here

| Plugin | Skills | What it is for |
|---|---|---|
| `brevo` | `brevo-automation-builder` | Builds a marketing automation in Brevo from a written briefing by driving the automation builder in Chrome. Confirms the account first, saves as an inactive draft, hands back a direct link. |

## How this fits with Plainpaper

Plainpaper ships a curated tool shelf per workspace — Brevo, Klaviyo, Meta Ads, Canva and so on. When
a board has one of those tools enabled, `get_board` hands the agent that tool's guidance: how a
Plainpaper card maps onto that platform, what to read before drafting, where to record what it did.

A tool can also declare that a skill in this repo carries the procedure its guidance deliberately does
not. Brevo's does:

> `brevo-automation-builder` — when the work involves an automation, workflow, flow, journey or drip
> sequence.

So an agent rehydrating a Brevo board that contains an automation is told the skill exists, when it
matters, and the two lines the human has to run to install it. Plainpaper cannot install anything into
your Claude client, which is exactly why it names the skill instead of trying.

None of that is required to use these skills. Every skill here works standalone, with or without a
Plainpaper connection.

## Adding a skill

See [CONTRIBUTING.md](CONTRIBUTING.md). The short version: one plugin per platform, named after the
same key Plainpaper uses for that tool, with one directory per skill inside it.

## Layout

```
.claude-plugin/marketplace.json        the catalog — every plugin listed here
plugins/
  brevo/
    .claude-plugin/plugin.json         plugin manifest
    skills/
      brevo-automation-builder/
        SKILL.md                       the instructions
        references/
          brevo-ui-map.md              UI map of the Brevo builder
          briefing-checklist.md        what a briefing needs to contain
```
