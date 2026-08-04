# Briefing checklist

Run through this as soon as the user describes a flow. Use what's there; ask once for what's missing and genuinely needed; pick something sensible for what's missing and small, and say so afterwards.

## Blocking — you can't proceed without these

**Trigger.** What starts the flow? For a list or segment trigger: which list exactly. For an event or website trigger: which event name or URL.

**The steps, in order.** Including every wait with a number and unit, and every branch with its condition.

**Email content per email step.** Existing template (name or ID) · existing automation message · or does something new have to be created? If new: from which template, with what content.

**Target lists and segments** for every step that adds, removes or filters contacts.

## Fillable — choose and report it

**Subject line and preview text.** If nothing is given, use the subject already in the template and say so.

**Sender name and address.** Take the account's default sender.

**Name of the automation.** Derive it from the purpose of the flow.

**Re-entry, exit and restart conditions.** If the briefing says nothing: leave everything off. That's the safest state and the easiest to switch on later.

**Send-time window and tracking.** Only touch if the briefing raises it.

## Verify against the account

Check every name from the briefing against what actually exists in Brevo (via the Brevo MCP tools if available). If something differs, don't guess at the nearest match — put the options you found in front of the user. A welcome flow on the wrong list is worse than waiting half an hour for an answer.

Do this after the account pre-flight in step 1, not before: looking up lists is only meaningful once you know which account you're in.

## Example of a complete briefing

> Flow: onboarding new newsletter subscribers.
> Starts as soon as someone joins the list "Nieuwsbrief NL".
> Immediately: welcome email, template #12, subject "Welkom bij Plainpaper", sender Thierry / hello@plainpaper.io.
> Wait 3 days.
> If the welcome email was opened: email with template #14, subject "Zo maak je je eerste board".
> If not: wait another 4 days and resend template #12 with subject "Nog even dit".
> Both branches end after that.
> Contacts who unsubscribe leave the flow.

From this you can lay down every step, configure all of them and set the exit condition without asking a single question.
