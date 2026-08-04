# Adding to this repo

## The one structural rule: one plugin per platform

A plugin is the unit somebody installs. A skill is a thing inside it. Those are not the same unit, and
getting the split wrong is the mistake that is expensive to undo later, because a plugin name is
baked into every install command anyone has ever run.

**One plugin per platform, named after the key Plainpaper already uses for that tool.** Plainpaper's
tool registry (`api/app/tools/registry.py`) keys Brevo as `brevo`, Meta Ads as `meta_ads`, Klaviyo as
`klaviyo`. Use the same word, hyphenated where the registry uses an underscore:

```
plugins/brevo/           <- Plainpaper tool key "brevo"
plugins/klaviyo/         <- "klaviyo"
plugins/meta-ads/        <- "meta_ads"
```

Two reasons this specific mapping, rather than a plugin per skill:

- **A second Brevo skill needs no second install.** Someone who installed `brevo` in June gets the
  campaign skill added in September by updating the marketplace, not by discovering a new plugin
  exists. Per-skill plugins push that discovery problem onto the user forever.
- **The Plainpaper side can name the plugin without a lookup table.** A board with the `brevo` tool
  enabled points at the `brevo` plugin. One fact, not two that can drift.

The cost is that installing `brevo` gives you every Brevo skill, including ones you will not use. That
is cheap — an unused skill costs its description in context and nothing else until it triggers.

### Skills that are not about a platform

Marketing tactics, frameworks, ways of working — things with no Plainpaper tool behind them — get
their own plugin in the same flat `plugins/` directory, named for what it is:

```
plugins/lifecycle-email/
plugins/creative-testing/
```

Do not call these **playbooks**. In Plainpaper, "playbook" is already the public name for a board
template — a pre-built board structure users import with a code. A second meaning of the same word,
in the same product, is a support conversation waiting to happen.

## Adding a skill to an existing plugin

```
plugins/<plugin>/skills/<skill-name>/
  SKILL.md                  required
  references/*.md           optional — anything long, loaded only when needed
  scripts/, assets/         optional
```

- The directory name and the `name:` in SKILL.md frontmatter **must match**.
- Use lowercase and hyphens: `brevo-automation-builder`, not `BrevoAutomationBuilder`.
- Prefix a platform skill with its platform. `automation-builder` on its own says nothing in a list of
  installed skills from four vendors.

### Writing the description

The `description:` is the only part of a skill that is always in the model's context. It is not a
summary — it is the trigger condition. Write what the user will be doing when this skill should fire,
in the words they will use, including the other languages they use. The Brevo skill lists Dutch
phrasings for exactly this reason.

State the negative too, when there is an obvious near-miss: Brevo's ends with "Do not use for
standalone Brevo email campaigns, templates or contact management that involve no automation."

### Writing the body

Read the existing `brevo-automation-builder/SKILL.md` before writing a new one. The conventions worth
copying:

- **Hard limits near the top**, phrased as never/ask-first, not as advice. Anything that sends,
  publishes, activates, charges or deletes belongs there.
- **Say what will fool you.** The Brevo builder re-renders slowly, so a screenshot taken immediately
  after a click shows the old state and the agent adds the same step twice. That single paragraph is
  worth more than the entire field reference next to it.
- **Long reference material goes in `references/`.** SKILL.md carries the procedure and the judgement;
  a table of every available trigger does not need to be in context to decide whether to start.
- **Never enter credentials.** If a platform is not logged in, the skill stops and asks the human. No
  skill in this repo may type a password, and none may accept one pasted into chat.

## Wiring a plugin to Plainpaper

Optional, and only meaningful for a plugin that maps to a Plainpaper tool. In the **plainpaper** repo,
add a `ToolSkill` to that tool's descriptor (`api/app/tools/<key>.py`):

```python
from app.tools.descriptor import ToolDescriptor, ToolSkill

BREVO = ToolDescriptor(
    key="brevo",
    ...,
    skills=(
        ToolSkill(
            name="brevo-automation-builder",
            plugin="brevo",
            use_when="the work involves an automation, workflow, flow, journey or drip sequence",
            summary="Builds the automation in Brevo's own builder, step by step, and leaves it inactive for review.",
        ),
    ),
)
```

`marketplace` and `source` default to this repo, so a skill living here declares neither. `get_board`
then surfaces the skill to any agent working on a board with that tool enabled, and the Tools page
shows the human its install command.

`name` must equal the skill's directory name and `plugin` the plugin's directory name — nothing
validates that across the two repos, so a typo simply sends the user an install command that fails.

## Releasing a change

1. Edit the skill.
2. Bump `version` in **both** `plugins/<plugin>/.claude-plugin/plugin.json` and the matching entry in
   `.claude-plugin/marketplace.json`. Nobody receives the change until that number moves.
3. Adding a whole new plugin means a new entry in `.claude-plugin/marketplace.json` as well.
4. Push.

## Before you commit

- [ ] Both JSON manifests parse, and the versions match each other.
- [ ] Skill directory name == frontmatter `name`.
- [ ] The description names the trigger conditions, not just the subject.
- [ ] Destructive or irreversible platform actions are listed as hard limits.
- [ ] No credentials, API keys or account ids anywhere in the skill or its references.
- [ ] The word "playbook" is not used for a skill.
