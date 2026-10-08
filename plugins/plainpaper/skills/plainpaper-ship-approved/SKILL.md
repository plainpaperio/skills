---
name: plainpaper-ship-approved
description: Ships approved work from a Plainpaper board to the platform where it runs, through that platform's own connector, and records where it landed. Use when someone asks to send, schedule, push, upload, publish or "get live" cards from their Plainpaper board, for example approved emails into Klaviyo, Mailchimp or Brevo, or approved ads into Meta Ads. Dutch too ("zet de mails in Klaviyo", "plan de mail in", "zet de ads live"). Not for drafting new work; use plainpaper-plan-campaign for that.
compatibility: Needs the Plainpaper MCP server (bundled with this plugin) plus the destination platform's own connector in the same client, for example Klaviyo, Mailchimp, Brevo or Meta Ads. Plainpaper never holds platform credentials and never sends anything itself.
---

# Ship approved cards to their platform

Plainpaper holds the work and the approval; the destination platform does the sending. You carry
the approved version across and record where it went.

## Before you push anything

1. **Only approved cards.** If a card is not `approved`, stop and ask the user to approve it on the
   board, or in this chat.
2. **Read it fresh.** Call `get_card` right before you push. The user may have edited the card since
   you last read it, and the approved text is the one on the board, not the one in this chat.
3. **Check `live_links`.** A card that already carries a link for this platform has been pushed. Ask
   before pushing it again, or you create a duplicate campaign.
4. **Confirm the audience and timing in chat** before anything is scheduled or sent: which list or
   segment, roughly how many recipients, and when. Create it as a draft when the user hasn't said to
   schedule or send.

## Images and files

Card content references media as `asset://<id>`. Fetch each one with `get_asset(asset_id,
mode="url")` and upload it to the platform's own media library through its connector, then use the
platform's URL. Plainpaper's links are short-lived and private, so never paste them into an email or
ad that runs later.

## Push, then record

1. Create the email, campaign or ad through the platform's connector, with the approved content.
2. Call `add_live_link(card_id, url, ...)` with the platform's link to what you created.
3. Once it is actually scheduled or live, set the card's status to `live`.

If the user has no connector for that platform in this client, say so and give them what they need
to do it by hand: the subject and body or ad copy, and where to download each image.
