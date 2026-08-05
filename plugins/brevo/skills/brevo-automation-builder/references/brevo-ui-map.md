# Brevo automation builder: UI map

Recorded by working through the live builder (August 2026). Brevo changes labels occasionally — trust what you see on screen, use this as a guide rather than gospel.

## Contents

- [URLs](#urls)
- [Account and login state](#account-and-login-state)
- [Editor layout](#editor-layout)
- [Available triggers](#available-triggers)
- [Available actions](#available-actions)
- [Available rules](#available-rules)
- [The create modal](#the-create-modal)
- [Configuration fields per step type](#configuration-fields-per-step-type)
- [Audience entry and exit](#audience-entry-and-exit)
- [What is and isn't automatable](#what-is-and-isnt-automatable)

## URLs

| Purpose | URL |
|---|---|
| Automations overview | `https://app.brevo.com/automation/automations` |
| Editor for one automation | `https://app.brevo.com/automation/edit/<id>` |
| Entry/exit settings | `https://app.brevo.com/automation/settings/<id>` |
| Email editor from a step | `https://app.brevo.com/editor/classic/html/<messageId>?automation_id=<id>&step_id=<stepId>` |

`https://automation.brevo.com/...` returns a 404. Always use `app.brevo.com`.

The editor URL is what you share with the user when you're done.

## Account and login state

The account name sits in the top right of the Brevo header, next to a building icon, with a chevron for switching accounts. On the automations overview it reads for example `Plainpaper`. That header is the fastest way to confirm which account you're in.

If not logged in, `app.brevo.com/automation/automations` redirects to a login screen. Ask the user to sign in; never enter credentials.

Brevo's own `accounts_get_account` verb gives the account behind the MCP connection (clients prefix MCP tool names differently — `mcp__Brevo__accounts_get_account` in Claude clients). That is not necessarily the same account as the browser session — the MCP uses an API key, the browser uses a cookie. Compare them before you start looking up lists and templates, because a mismatch means every lookup silently describes a different account than the one you're clicking in.

## Editor layout

Header: automation name + chevron (Rename / Assign a tag / See details / Delete automation) · undo/redo · "Saved today at HH:MM" · status badge (Inactive/Active) · **Activate automation** (never click) · **Test** (sends real email) · **Exit editor**.

Left rail: **Builder** · **Settings** (Audience entry and exit) · **Activity**.

Side panel in Builder: search field "Search by step name" + three tabs **Triggers** / **Actions** / **Rules**. The cards are `draggable` and the help text says "drag it to the canvas", but **clicking works too and is the route to take**:

- Empty canvas → the step becomes the first step immediately and the configuration panel opens.
- Canvas with steps → a banner appears at the top, "Select a spot to add the step to the canvas", with a Cancel link, and a clickable **"Add Step here"** label appears at every valid position. After you choose, the configuration panel opens.

An empty slot below the trigger is labelled **"Drop block here"** — you fill that by clicking the card too, not by dragging.

Canvas on the right: React Flow. Zoom controls bottom right, including **"Fill to view"** (fits the whole flow on screen). Every step has a ⋮ menu with **Duplicate** and **Delete** — nothing else. There are no "+" buttons on the connecting lines.

Click a step and clickable **"Move step here"** labels appear at every valid position, including inside the other branches.

All four have been tested and work through automation:

| Action | How | What happens |
|---|---|---|
| Add | click card in side panel → "Add Step here" at the spot you want | Step appears there, configuration panel opens immediately |
| Move | click the step → "Move step here" | Step jumps to that position, autosave follows. Attached configuration (linked message, subject) is preserved |
| Duplicate | ⋮ → Duplicate | Copy directly below the original, same settings, new Step ID. Toast "The step has been duplicated" |
| Delete | ⋮ → Delete | Gone immediately, **no confirmation dialog**. Toast "The step has been deleted". Undo arrow in the header reverses it |

The trigger itself can also be deleted (toast "The trigger has been deleted") and replaced with a different one.

A step that isn't configured yet shows orange "Define and save this action." (actions/rules) or "Verify and save this trigger." (triggers).

## Available triggers

**Contacts**: Contact added to list · Contact removed from list · Contact matches custom filters · Contact is in a segment · Anniversary · Contact added manually
**Forms**: Form submitted
**WhatsApp**: WhatsApp reply received
**Email**: Email opened · Link clicked in an email · Unsubscribed from emails
**Conversations**: Conversation started · Conversation ended · Message received · Sales email opened · Sales email clicked
**Deals**: Deal created · Deal stage updated · Task created · Task completed
**Website**: Webpage visited
**Custom**: Custom event
**Coupon**: Unique coupon sent

## Available actions

**Contacts**: Add contact to a list · Remove contact from a list · Update contact attribute · Blocklist contact · Assign a user to a contact · Delete a contact · Update company attribute
**Messaging**: Send an email · Send an SMS · Send a one-to-one email from a mailbox · Send a WhatsApp message · Notify by email
**Webhooks**: Call a webhook
**Deals**: Create a task · Update deal attribute · Duplicate a deal · Create a deal
**Automations**: Start another automation · Redirect contact to another step

## Available rules

Time delay · Conditional split · Percentage split · Wait until an event happens

Some items carry a crown icon: those sit in a higher plan and may not be usable.

## The create modal

"Create an automation" opens a modal with three things in it. Only one of them is useful here.

**"Create from scratch"** (top right) — what you want. Drops you into an empty editor.

**The AI generator** — "Start with a sentence, we'll build your automation", with a *Guided mode* / *Write your own* toggle and a text box. Brevo's assistant is called Aura. It works and it handles Dutch: a description like *"Wanneer een contact wordt toegevoegd aan een lijst, wacht 1 dag en stuur een welkomstmail. Wacht daarna 3 dagen; als het contact de mail heeft geopend stuur een tweede mail, anders voeg het contact toe aan een andere lijst"* produced a correct skeleton (trigger → Wait 1 day → Send an email → Wait 3 days → Conditional split with two branches → Exit) in about 45 seconds, all steps as empty placeholders, automation left Inactive.

Skip it anyway. You still have to configure every step by hand afterwards, so the only thing it saves is a handful of clicks — and in exchange it puts its own reading of the briefing on the canvas, which you then have to verify and correct. It also names the automation itself (something like "Welcome and Re-engagement Sequence #4"), shows a "Does this automation match your goal?" telemetry card, and leaves a stray draft behind if the result is unusable. Documented here so you recognise it, not so you use it.

**Pre-built automation type** — a dropdown (Most popular / All / Improve engagement / Increase traffic / Increase revenue / Build Relationships) with a Channel filter and template cards: Welcome message, Abandoned cart, Marketing activity, Product purchase, Anniversary date. Same objection as the generator: someone else's structure that you have to bend back into the briefing.

## Configuration fields per step type

### Trigger: Contact added to list
One field **List** (required). Dropdown with folders (e.g. "Main"); open the folder or type in the field to search. At the bottom: "Create new contact list". Info notice: the trigger only counts contacts added after activation.

### Rule: Time delay
Four numeric fields side by side: **Months · Days · Hours · Minutes**. One month = 30 days.

### Rule: Conditional split
Per branch a block with a pencil to rename the branch and an **Add filter** button. That opens the "Define split conditions" modal with:

- **Select a list or segment** — dropdown
- **Add filter** — dropdown with a search field and categories: Contact details · Email · SMS · coupon · Conversations · Deal Events · Forms · Deal · (more on scroll)

**Save conditions** at the bottom. The last branch is always "Contact does not match any of the defined conditions" and takes no filter. **Add Branch** adds branches (possibly paid).

Contacts take the first branch whose condition they match.

### Action: Send an email

**Content → Message** (required)
"Add message" with two options:
- *Create new message* — opens the "Create new message" picker. Left: "Create from scratch" (with a chevron for editor choice), "Your emails → Automation messages", "Templates → Your templates / Basic templates / Ready-to-use". Right: a grid of previews, each with an ID (`#10`), a name and a **Use template** button. A "Search by name or ID" field and a Sort by sit on top.
- *Sync with an existing message* — links to a message already used in another step of this automation; edits then apply to every linked step.

After "Use template" the email editor opens (classic HTML editor or drag-and-drop editor, depending on the template). Return with **"Use this design in automation"** in the top right. The panel then shows the preview with `#id` and **Edit / Preview / trash** buttons.

**What event data to display?** — dropdown; defaults to "The email has no event variables". Only relevant for event-triggered emails with variables.

**Subject → Subject line** (required) with an emoji picker, `{}` for personalisation fields and "Write with AI". **Preview text** optional, same buttons.

**Sender → Email address** (dropdown of verified senders) + **Sender's name** (text field).

**Additional settings → Edit settings** — send time, tracking, subscription.

### Action: Add contact to a list
List dropdown with the same folder structure as the trigger.

## Audience entry and exit

Three blocks:

- **Contact re-entry after exit** — toggle "Allow contact re-entry after exit" + toggle "Set up wait time".
- **Exit conditions** — "Add exit condition" (dropdown).
- **Restart conditions** — "Add restart condition" (dropdown).

**Save conditions** in the top right. Everything is off by default.

## What is and isn't automatable

**Works reliably**: everything you do by clicking and typing — adding steps via the card click, selecting steps, form fields, dropdowns, search fields, modals, Save buttons, "Add Step here", "Move step here", Duplicate, Delete, renaming, zooming.

**Doesn't work, and isn't needed**: dragging. Both `left_click_drag` and JavaScript-synthesised HTML5 drag events are ignored, even though `dragstart` → `dragenter` → `dragover` → `drop` fire correctly on the right target and the payload (`automation/draggedcarddata`) sits correctly in the DataTransfer — the app only accepts real, user-generated events. Don't try; just click the card.

**The canvas re-renders slowly.** After adding, moving or deleting, it can take 2-3 seconds before you see it. A screenshot taken right after the click often still shows the old state. Wait and look again before concluding nothing happened — otherwise you add the same step twice. (This is exactly the trap the first version of this skill fell into: the conclusion "clicking does nothing" came from a screenshot taken too fast.)

**Escape closes the whole modal**, not just an open dropdown. To close only a dropdown, click next to the field.
