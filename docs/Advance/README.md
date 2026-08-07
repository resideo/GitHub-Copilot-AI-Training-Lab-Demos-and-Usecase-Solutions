# CP-advanced Demo Pack

This folder now contains two layers of material for the advanced course demo
pack:

- One customer-facing handout for post-session sharing
- Three presenter runbooks with starter assets for the live demos

## Customer-facing handout

- [Client handout](client-handout.md)

## Presenter runbooks

| # | Runbook | Deck checkpoint | Focus | Primary takeaway |
| --- | --- | --- | --- | --- |
| 1 | [demo-1-legacy-test-generation/demo.md](demo-1-legacy-test-generation/demo.md) | Slide 34 / Demo 5 | Generate tests from legacy Python rules | Ask for a branch map before asking for tests |
| 2 | [demo-2-migration-parity/demo.md](demo-2-migration-parity/demo.md) | Slide 34 / Demo 5 | Port a Python parser to C# while preserving behavior | Demand invariants and parity evidence before code |
| 3 | [demo-3-modernization-observability/demo.md](demo-3-modernization-observability/demo.md) | Slide 34 / Demo 5 | Use GitHub Copilot upgrade plus refactoring on a .NET worker | Modernize in phases instead of rewriting blindly |

## Recommended presenter setup

1. Open the advanced deck, this index, and the selected `demo.md` runbook side
  by side.
2. Open the starter code file before the session so the room sees the baseline.
3. Keep one scratch editor ready for generated outputs such as a test file, a
  translated class, or a phased refactor plan.
4. End each demo by showing the validation artifact, not just generated code.

## What to share after the session

1. Share [client-handout.md](client-handout.md) as the primary customer-facing
  document.
2. Include the relevant demo folder if the client wants the exact starter files
  and prompts used in the live walkthrough.
3. Add any generated output files from the session as separate examples, while
  keeping the starter files unchanged.

## Validation commands

```bash
# Demo 1 starter file
python -m py_compile demo-1-legacy-test-generation/thermostat_rules.py

# Demo 1 after Copilot generates the test file
python -m unittest -q demo-1-legacy-test-generation/test_thermostat_rules.py

# Demo 2 starter file
python -m py_compile demo-2-migration-parity/legacy_schedule_parser.py

# Demo 3 starter project
dotnet build demo-3-modernization-observability/DeviceSyncWorker.csproj
```

## Presenter note

These are presenter-led runbooks, not attendee labs. If time is short, keep the
prompt flow, show one validation artifact, and skip optional extensions.
