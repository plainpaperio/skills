---
name: brevo-automation-builder
description: Builds a complete marketing automation in Brevo by clicking through the automation builder in Chrome, based on a briefing from the user. Use this skill whenever someone wants to build, set up, configure or finish a Brevo automation, workflow, flow or customer journey — a welcome flow, onboarding series, abandoned cart, re-engagement, birthday email, lead nurturing sequence, and so on, in Brevo (formerly Sendinblue). Also trigger when the user simply shares a briefing or description of an email flow and asks whether it can be "put into Brevo", or asks to modify, reorder or complete an existing Brevo automation. Requests often arrive in Dutch ("automation bouwen", "flow inrichten", "welkomstflow in Brevo") — treat those the same. Do not use for standalone Brevo email campaigns, templates or contact management that involve no automation.
---

# Build a Brevo automation from a briefing

You build a workflow in Brevo's automation builder that does exactly what the briefing says, and you hand it over as a **draft (Inactive)** so the user can review it and switch it on themselves.

The builder is a React Flow canvas. The side panel says "drag it to the canvas" and dragging genuinely does not work through browser automation — but you don't need it: **clicking a card in the side panel adds the step.** On an empty canvas it becomes the first step; if steps already exist, the builder enters placement mode (banner "Select a spot to add the step to the canvas") and a clickable **"Add Step here"** label appears at every valid position, so you pick the spot yourself. Everything in this builder is reachable by clicking and typing.

One thing will fool you a few times: **the canvas re-renders slowly.** A step you just added sometimes only appears 2-3 seconds later. Never conclude from a single immediate screenshot that nothing happened — wait and look again. Otherwise you add the same step twice.

## Hard limits

- **Never click "Activate automation".** The automation stays Inactive. Say this back to the user if they ask you to make it live: that is their click, after review.
- **Never "Delete automation"** without explicit permission in chat.
- **Deleting a step** is fine inside an automation you are building in this session — that's just correcting yourself, and the undo arrow catches mistakes. In an automation the user already had, ask first. There is no confirmation dialog, so check which step's ⋮ menu you have open.
- **Never send email.** "Test" in the header sends real test messages — only use it if the user asks for it in chat.
- Leave existing, active automations alone unless the user points at one explicitly.

## Step 1 — Pre-flight: which account are we in?

Before you build anything, establish where you'd be building it. Getting this wrong means a welcome flow landing in the wrong client's account, which is the kind of mistake that is expensive to explain afterwards.

Load the browser tools in a single ToolSearch call:

```
select:mcp__claude-in-chrome__tabs_context_mcp,mcp__claude-in-chrome__navigate,mcp__claude-in-chrome__computer,mcp__claude-in-chrome__browser_batch,mcp__claude-in-chrome__find,mcp__claude-in-chrome__get_page_text
```

Open `https://app.brevo.com/automation/automations` and take a screenshot. Three things can happen:

**Not logged in** — you land on a login screen. Stop and tell the user, in one line, that Brevo isn't logged in in their Chrome and that they need to sign in themselves; never enter credentials. Offer to continue the moment they say they're in.

**Logged in** — the account name sits in the top-right of the header, next to a building icon (e.g. "Plainpaper"), with a chevron for switching accounts. Read it. Also note how many automations already exist and whether any are Active, so you don't confuse an existing flow with your own later.

**Logged into more than one account** — the chevron lists them. Don't switch accounts on your own initiative; ask.

If the Brevo MCP tools are available, also call `mcp__Brevo__accounts_get_account`. Compare it against the account in the browser header. If they differ, say so and ask which one is authoritative — the MCP would then be reading lists and templates from a different account than the one you're clicking in, and every lookup you do would be quietly wrong.

Then confirm with the user before touching anything, using AskUserQuestion (or plain text if that tool isn't available):

> I'm about to build the automation in Brevo account **Plainpaper** (logged in as thierry@…). It'll be saved as a draft and left inactive. Shall I go ahead?

Wait for a yes. Two exceptions: if the user already named the account in this conversation, just state which account you're using and continue; and if the session is clearly unattended (scheduled run, user said they'd check back later), state the account at the top of your report and proceed rather than blocking on a question nobody is there to answer.

## Step 2 — Complete the briefing

Read the briefing and check it against `references/briefing-checklist.md`. Four things you need before you touch anything; the rest you can fill in as you go:

1. **The trigger** — what starts the flow?
2. **The steps, in order** — including waits and branches.
3. **Content per email step** — an existing template (name or ID), an existing automation message, or something that still has to be created?
4. **Which list or segment** wherever that applies.

If something essential is missing, ask it in one go via AskUserQuestion rather than drip-feeding questions step by step. If only something small is missing (a subject line, a sender name), pick a sensible option and report it afterwards.

### Look up account data through the MCP, not by clicking

If the Brevo MCP tools are available, use them to look up lists, templates and senders. It's faster and more reliable than scrolling through dropdowns, and you need the exact names and IDs to click the right item later:

- `mcp__Brevo__lists_get_lists` — list names and IDs
- `mcp__Brevo__segments_get_segments` — segments
- `mcp__Brevo__templates_get_smtp_templates` — saved templates with their ID (the builder shows those IDs too, as `#10`)
- `mcp__Brevo__senders_get_senders` — valid sender addresses

Match the briefing's names against these. If a name differs (briefing says "Nieuwsbrief", the account has "Newsletter NL"), don't guess at the nearest match — put the options in front of the user.

## Step 3 — Put the structure in place

Go to `https://app.brevo.com/automation/automations` and click **"Create an automation"** in the top right. The modal offers an AI generator ("Guided mode" / "Write your own") and a set of pre-built templates. Ignore both and click **"Create from scratch"** in the top right of the modal.

The AI generator does work — it turns a description into a full skeleton in about 45 seconds — but it costs you more than it saves. It inserts its own interpretation between the briefing and the canvas, which you then have to check and correct step by step, and correcting is slower than building it right the first time. Adding a step yourself is two clicks. Build it yourself and you know exactly what's on the canvas and why.

You land in an empty editor. Build the flow step by step:

1. Pick the **Triggers**, **Actions** or **Rules** tab on the left — or type in the "Search by step name" field, which is faster than scrolling categories.
2. Click the card. On an empty canvas the step appears immediately; otherwise placement mode starts and you click the "Add Step here" label at the position you want.
3. The configuration panel opens by itself. Fill it in and click **Save** (step 5 has the fields per step type).
4. After each addition, wait a few seconds and take a screenshot before moving on. The canvas lags, and adding a step twice is easier done than undone.

You can configure each step as you add it, or lay down the whole skeleton first and then configure top to bottom. Adding everything first has one advantage worth having: you see the complete shape against the briefing before you invest time in filling in fields.

## Step 4 — Check the structure before configuring

Click **"Fill to view"** (the icon near the zoom controls, bottom right) so the whole flow is visible, and take a screenshot. Walk the branches against the briefing.

If the structure is right, go to step 5. If it isn't, you have four tools and they all work:

- **Add a step** → click the card in the side panel → click the **"Add Step here"** label where you want it.
- **Move a step** → click the step. A clickable **"Move step here"** label appears at every valid position, including inside the other branches. Click the right one; the step jumps there and the automation saves itself. Configuration already attached to the step (a linked message, a subject line) survives the move.
- **Copy a step** → ⋮ → **Duplicate**. The copy lands directly below with the same settings and a new Step ID. Handy when a second email is nearly identical to the first: duplicate and change only the subject, and you skip the whole template picker.
- **Delete a step** → ⋮ → **Delete**. Goes through immediately, **no confirmation dialog**. The undo arrow in the header reverses it.

So there is no structural change you cannot make. If something appears not to work, the slow canvas is the first suspect — wait and look again before trying it a second time.

The same applies to existing automations: a flow the user built earlier can be reordered, extended and cleaned up. Just don't touch an active automation unless the user asks for it in chat.

## Step 5 — Configure every step, top to bottom

Click a step on the canvas → the configuration panel opens on the left → fill it in → **Save** at the bottom. As long as a step shows orange "Define and save this action" or "Verify and save this trigger" underneath it, that step isn't finished.

Work top to bottom, and save each step before opening the next. Clicking Cancel loses everything in that panel.

Exact fields per step type are in `references/brevo-ui-map.md`. The essentials:

- **Trigger "Contact added to list"** — one field: List. The dropdown shows folders (e.g. "Main"); open the folder to see the lists, or type to search.
- **Time delay** — four numeric fields: Months, Days, Hours, Minutes. One month counts as 30 days.
- **Send an email** — the most involved one; see below.
- **Conditional split** — per branch "Add filter" → pick a category (Contact details, Email, SMS, Forms, Deal Events…) or select an existing list/segment → Save conditions. The last branch is always "does not match any condition" and needs no filter. Contacts take the first branch whose condition they match, so branch order carries meaning.
- **Add contact to a list / Update contact attribute** — dropdown with the same folder structure.

### The email step

Under **Message**, click "Add message":

- **"Sync with an existing message"** if the same email is already used elsewhere in this automation. Preferred, because it keeps one source of truth.
- **"Create new message"** otherwise. A picker opens with "Automation messages" (previously created automation emails), "Your templates", "Basic templates", "Ready-to-use", plus "Create from scratch". Search by name or ID and click **"Use template"**.

The email editor then opens in the same tab. Don't change the content unless the briefing asks for it — click **"Use this design in automation"** in the top right. You return to the automation with the message attached (recognisable by a `#number` next to the preview).

Then scroll further down the panel and fill in:

- **Subject line** (required) — from the briefing. The field often already holds the template's own subject; overwrite it.
- **Preview text** — if the briefing provides one.
- **Sender: Email address + Sender's name** — check against the briefing; the default isn't always the right sender.
- **Additional settings → Edit settings** — only touch if the briefing mentions send-time windows, tracking or subscription.

Click **Save**.

## Step 6 — Name, settings, and hand-over

**Rename**: click the chevron next to the title → Rename → type the name → checkmark. Give it a name the user will recognise, taken from the briefing.

**Audience entry and exit** (the "Settings" icon in the left rail, or `app.brevo.com/automation/settings/<id>`): only fill in if the briefing says something about re-entry, exit conditions or restarts. Everything is off by default, which is the safest state. Click "Save conditions" if you change anything.

**Final check**, and do this before you report anything back:

1. "Fill to view" and screenshot: is there orange text left anywhere?
2. Does every step match the briefing — list, wait, template, subject line, sender, conditions?
3. Is the badge in the top right still **Inactive**?
4. Does the header say "Saved today at HH:MM"? The builder autosaves, but this is your confirmation.

**Then share the link, immediately.** The moment the automation is finished, give the user the direct URL — `https://app.brevo.com/automation/edit/<id>` — as the first thing in your report, not buried at the end. The whole point of handing over a draft is that they go and look at it, and a link they have to hunt for is friction you can remove. If a false start left an extra draft behind, name that one too so nothing unexplained shows up in their overview.

Then report: a screenshot of the canvas, what you configured per step, every assumption you filled in yourself, and what the user still has to do before activating.

## If the briefing came from a Plainpaper board

Skip this section if Plainpaper isn't connected — everything above works on its own.

When the user points you at a Plainpaper board, or the Plainpaper MCP tools are available and the
briefing plainly came from one, the board is the briefing and it is worth reading properly before you
open Brevo. `get_board` gives you the whole campaign in one call; the plan or automation card carries
the flow, and the email cards linked to it carry the content each step sends. Two things to respect
while reading:

- **Only build from cards the human has approved.** `status` is the go-ahead. A card still in `draft`
  is someone thinking out loud, not an instruction.
- **A card body that still contains a `placeholder://` token is unfinished** — that is an image slot
  nobody filled yet. Say so rather than building a step around it.

Then build the automation exactly as described above. Nothing about the Brevo side changes.

**When you're done, record where it landed.** The board is meant to answer "where did this go" months
later, and it can only do that if you write it back:

```
add_live_link(card_id=<the card the automation came from>,
              url="https://app.brevo.com/automation/edit/<id>",
              label="Brevo automation",
              platform="brevo")
```

Use the id from the URL of the automation you just built — copy it from the address bar, never
reconstruct it from memory. Plainpaper checks the shape and refuses a Brevo URL it doesn't recognise,
which is deliberate: a plausible invented link is indistinguishable from a real one once it is on the
board. Recording a link does not change the card's status, and it shouldn't — what you handed over is
still an inactive draft waiting for a human to switch it on.

## When things go wrong

If a click misses the same element three times, stop hammering. `find` with a natural-language description often gives you a usable reference where coordinates fail; `get_page_text` shows what's actually on the page. If that doesn't help, report what you tried, what you saw and what you still need — with the automation in whatever state you left it.

If you hit a 404 on `automation.brevo.com`, use `app.brevo.com/automation/automations` instead. If the user turns out not to be logged in, ask them to log in themselves; never sign in with credentials.
