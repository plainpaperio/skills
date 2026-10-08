---
name: plainpaper-plan-campaign
description: Plans and builds a marketing campaign on the user's Plainpaper board, card by card, for them to review and approve. Use when someone wants to plan, set up or run a campaign while Plainpaper is connected, for example a product launch, a welcome or onboarding email series, a winback or post-purchase flow, a promotion or sale, a content calendar, a giveaway, a quiz funnel, a paid social or Meta ads test, positioning or competitor research, or just "help me with my marketing". Also use when they ask to put a plan, emails or ads "on a board" or "in Plainpaper". Dutch requests count too ("campagne opzetten", "lancering plannen", "welkomstmails schrijven", "zet het op een bord"). For Black Friday or Cyber Monday use plainpaper-black-friday. To pick up or review a board that already exists, use plainpaper-continue-board.
compatibility: Needs the Plainpaper MCP server (https://mcp.plainpaper.io/mcp), bundled with this plugin. The user signs in to their own Plainpaper account; the free plan holds two boards.
---

# Plan a campaign on a Plainpaper board

The user gets a board they can watch fill up live: the brief, the audience, the plan, every email and
every ad as separate cards, each one waiting for their approval. You do the work, they steer. Nothing
leaves Plainpaper until they approve it, and Plainpaper itself never sends or posts anything.

## 1. Ask Plainpaper which playbook fits

Call `plan_campaign` with the goal in the user's own words, for example
`plan_campaign(goal="launch our new winter boot in November")`. It returns:

- `recommended`: the playbook that fits, why (`matched`), the questions to ask first (`ask_first`) and
  whether it needs a launch date (`needs_launch_date`)
- `alternatives`, `active_boards`, `board_limit` and sometimes an `onboarding` play for a new workspace
- `next_step` and `working_rules`. Follow the working rules for the whole conversation.

If the user has several workspaces (separate brands or clients), ask which one this is for before
you build, and pass its `workspace_id`.

## 2. Agree on the start in one short message

- Say in one sentence which playbook you picked and why, in plain words. Don't list every option.
- Ask the `ask_first` questions together, conversationally, and the launch date if
  `needs_launch_date` is true. Skip anything the user already told you. Partial answers are fine; a
  playbook falls back to its own wording.
- **Then stop and wait for their reply.** Create the board only after they answer, or after they
  say to go ahead without answers. Their answers are written into the board's brief, which every
  later conversation reads, so they are worth one round trip.
- If an existing board in `active_boards` is clearly the same campaign, offer to continue there.
- If `board_limit` shows no free slot, don't try to create a board. Offer to continue on an existing
  one, or ask them to archive one in Plainpaper.

## 3. Build the board

1. `create_board_from_template(template_id, name, anchor_date, intake)`, with `intake` keyed by each
   question's `key`. Use a name the user would recognise ("Winter boot launch").
2. Share the board link straight away ("Your board is ready, you can watch it fill up here: <url>")
   and call `show_board(board_id)` once. In Claude it puts the live board right in the
   conversation, where it updates itself as you write; don't call it again for that.
3. If the board has `use_guidelines` on, call `get_guidelines` before you write and follow it. If the
   workspace has no guidelines yet and the user has a website, offer to draft them first; don't insist.
4. `get_board` to see the seeded phases and cards, then work through them in order. Replace each
   placeholder with work specific to this business. One idea per card, short titles, bodies a
   marketer would actually use.
5. Emails are `email_campaign` cards and ads are `creative` cards: read `get_deliverable_type` once
   for the exact fields before you write the first one.
6. Connect cards that inform each other with `link_cards`, so the reasoning stays visible (insight
   informs the offer, the offer informs each email).
7. When a card is finished from your side, set it to `awaiting_approval`. That is what puts it in
   front of the user. Never set `approved`, `live` or `done` unless the user says so in the chat.

Keep the chat short while you build: a line per phase is enough, the board is where the work lives.

## 4. Hand over

End with what's on the board, what is waiting for their approval, and the one decision that matters
most right now. Tell them they can close this chat: in any new conversation you can pick the campaign
up from the board.

## Things that go wrong

- **Writing everything into the chat instead of the board.** The board is the deliverable. Put the
  work on cards.
- **Generic filler.** A card that would fit any brand is a card the user has to rewrite. Use what
  they told you, and ask when you don't know.
- **Leaving finished cards in `draft`.** The user only reviews what is `awaiting_approval`.
- **External image links in a card.** They're rejected. Ingest media with `upload_asset_from_url`
  and reference the returned `asset://<id>`, or use a `placeholder://name` slot for an image that
  doesn't exist yet.
