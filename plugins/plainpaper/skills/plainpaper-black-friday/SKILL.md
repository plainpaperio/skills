---
name: plainpaper-black-friday
description: Plans a Black Friday and Cyber Monday (BFCM) campaign on the user's Plainpaper board, with the offer, the email sequence and the ads written and approved before the week itself. Use whenever someone mentions Black Friday, Cyber Monday, BFCM, Cyber Week, the holiday sale or their Q4 promotion while Plainpaper is connected, including Dutch phrasings ("Black Friday actie", "Black Friday campagne", "Cyber Monday mails"). For other seasonal sales without a Black Friday angle, use plainpaper-plan-campaign.
compatibility: Needs the Plainpaper MCP server (https://mcp.plainpaper.io/mcp), bundled with this plugin. The user signs in to their own Plainpaper account.
---

# Plan Black Friday and Cyber Monday on a Plainpaper board

Black Friday is won in the weeks before it. The aim is a board where the offer is decided, every
email and ad is written and approved, and the week itself only needs a send button.

**Dates.** In 2026, Black Friday is Friday 27 November and Cyber Monday is 30 November. Work out how
many days are left from today and say it once: it sets how compressed the plan is. Use Black Friday
as the board's launch day (`anchor_date`).

## 1. Pick the board

Call `plan_campaign(goal=...)` with the user's words. Then:

- If it recommends `official/bfcm-backwards-plan`, use it: ask its `ask_first` questions and create
  it with `anchor_date` set to Black Friday. Follow its pinned guidance; it is the full method.
- Otherwise (the BFCM playbook is on paid plans), it recommends a general campaign board. Create
  that one with `anchor_date` set to Black Friday and shape it into the sprint below.

Share the board link as soon as it exists.

## 2. The sprint (for the general board, or with little time left)

Ask the user, in one message: what they sell, last year's Black Friday in one line, the number that
would make this one a success, and the deepest discount that still makes money. Then build, as cards:

1. **The goal and the floor.** One number to hit, and the margin floor below which a discount loses
   money. Every offer decision gets checked against this card.
2. **The offer and the cut list.** What is on offer, what is deliberately excluded, and why. A
   precise offer beats a site-wide percentage.
3. **Who hears what.** Past buyers and VIPs (early access), engaged subscribers, and lapsed customers
   each get a reason to care. One audience card each.
4. **Five emails** (`email_campaign` cards, each timed with `set_card_timing` relative to Black
   Friday):
   - T-7: teaser and early-access signup
   - T-1: early access for past buyers and VIPs
   - T0: launch, Black Friday morning
   - T+2: weekend reminder
   - T+3: Cyber Monday last call
   Each with subject, preheader, body and one call to action.
5. **Ads** (`creative` cards): two or three that carry the same offer, plus one retargeting ad for
   site visitors and abandoned carts.
6. **The checklist.** Site banner, discount codes, stock on the hero products, shipping cutoffs,
   support answers. One card, a short list.
7. **Results.** What to measure afterwards (revenue against the goal, conversion rate, revenue per
   email sent, average discount given), linked to the goal card with `link_cards` type
   `measured_by`.

Link the offer to every email and ad (`informs`), so the reasoning stays on the board.

## 3. Approval and after

Set each finished card to `awaiting_approval` and say which ones need a decision first: the offer and
the floor come before any copy. Never set `approved` yourself unless the user says so in the chat.
Once cards are approved, the plainpaper-ship-approved skill covers getting them into Klaviyo,
Mailchimp, Brevo or Meta Ads through those platforms' own connectors.
