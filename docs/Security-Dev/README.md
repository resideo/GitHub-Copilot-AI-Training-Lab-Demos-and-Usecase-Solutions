# CP-Security-Dev - Demos

Four presenter-led demo runbooks aligned to the CP-Security-Dev deck.

These demos are designed for a 2-hour security session with no attendee lab. Each runbook is short,
safe, and focused on one teaching point so the presenter can keep momentum without improvising.

## Demo inventory

| # | Runbook | Deck checkpoint | Focus | Runtime |
| --- | --- | --- | --- | --- |
| 1 | [demo_01_prompt_hygiene.md](demo_01_prompt_hygiene/demo_01_prompt_hygiene.md) | Demo 1 | Least context, placeholders, retained-log mindset | 6-8 min |
| 2 | [demo_02_repository_controls.md](demo_02_repository_controls.md) | Demo 2 | Branch protections, required checks, CODEOWNERS | 8-10 min |
| 3 | [demo_03_responsible_ai_review.md](demo_03_responsible_ai_review.md) | Demo 3 | AI review as signal, human review as gate | 8-10 min |

## Recommended presenter setup

- Open the live deck and [SPEAKER_NOTES.md](../SPEAKER_NOTES.md) side by side.
- Open all four runbooks in a second editor group before the session starts.
- Use one safe sample repository or scratch workspace for prompts and review examples.
- Never paste live secrets, customer records, incident data, or regulated artifacts.
- Prefer screenshots for repository settings if your training repository does not expose the right
  rulesets live.

## Shared facilitation pattern

1. Frame the risk or decision first.
2. Show the unsafe or incomplete version without executing it.
3. Move to the safe prompt, safe workflow, or safe control.
4. Ask the room what changed and why it matters.
5. End with the one sentence they should remember.

## Presenter note

These are deliberately presenter-led runbooks, not attendee exercises. If time runs short, keep the
demo prompt and skip the optional extensions.
