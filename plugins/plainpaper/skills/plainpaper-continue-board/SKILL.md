---
name: plainpaper-continue-board
description: Picks a campaign back up from the user's Plainpaper board in a fresh conversation, reports what is waiting for their approval, acts on their comments and continues the work. Use when someone says "continue my campaign", "where were we", "what's waiting for me", "pick up the launch board", "check my Plainpaper", asks how a campaign is going, or mentions a board, card or comment in Plainpaper. Dutch too ("waar waren we", "ga verder met mijn campagne", "wat staat er klaar"). For starting a new campaign, use plainpaper-plan-campaign.
compatibility: Needs the Plainpaper MCP server (https://mcp.plainpaper.io/mcp), bundled with this plugin.
---

# Continue a campaign from the Plainpaper board

The board is the memory. Everything from earlier conversations is on it: every card, how the cards
connect, their status, and the user's comments. Don't ask the user to re-explain anything the board
already says.

## 1. Find the board

`list_boards()` with no workspace lists boards across every workspace the user connected. Pick the
one they mean; if two could fit, ask with the names. Prefer active boards over archived ones.

## 2. Read it back

- `get_board(board_id)` gives the whole campaign without card bodies: phases, cards with status and
  summary, links, timing, and the board's pinned brief. Use `get_card` only for the cards you need
  in full.
- `list_comments(board_id)` gives the user's open comments, each anchored to a card or a phase.
  Comments are instructions.

## 3. Report in a few lines

- What is waiting for their approval (`awaiting_approval`), named so they can find it
- Open comments you are about to act on
- What is approved but not yet live
- The one decision that would unblock the most, with the board link

Then call `show_board(board_id)`: in Claude the user can review and approve the waiting cards right
there in the conversation.

## 4. Continue

- Act on each comment: revise the card with `update_card` (pass the `expected_version` you read; on
  a `version_conflict`, read the card again and redo the edit), set it back to `awaiting_approval`,
  then `resolve_comment`.
- Carry on with the next cards in phase order, setting each finished one to `awaiting_approval`.
- An approval the user gives in this chat counts: then you may set `approved`. Silence never does.
- If the board has `use_guidelines` on, call `get_guidelines` before writing.
