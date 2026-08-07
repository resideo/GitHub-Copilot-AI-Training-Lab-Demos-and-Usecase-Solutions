---
title: Demo 2 - Model Comparison
layout: default
parent: Demos
grand_parent: Prompt Engineering
nav_order: 2
permalink: /prompt-engg/demos/demo-2.html
description: "Compare identical prompts across models, cost, and context quality."
---

# 🐍 Demo 2 — Model Comparison: Same Prompt, Different Models
{: .no_toc }

**Deck checkpoint:** Foundations demo (slide 14 — *"Same prompt, different models — compare answers and token cost"*)

**Language:** **Python**

**Duration:** ~10–13 minutes · **Prompts:** 1 shared prompt run 4+ ways + 3 follow-ups

**Covers:** Auto vs fast vs reasoning models · same prompt / different answers · **token cost** · when Auto is enough · context as the great equalizer

> **This is the flagship demo.** Hold the prompt, the file, and the context **constant**, change only
> the **model**, and let the room see how the answer, the depth, and the **token usage** change. Then
> show that good context lets a cheaper/faster model match a reasoning model — for fewer tokens.

{: .highlight }
> **No clone required.** Copy the two files below into a folder named
> `demo-2-model-comparison`, then follow the steps.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Files

| File | Purpose |
|---|---|
| `sensor_alerts.py` | The code each model extends — `summarize_severity_mix` is the target |
| `alerting-policy.md` | The business context we attach in Part C |

### `sensor_alerts.py`

```python
from dataclasses import dataclass


@dataclass(frozen=True)
class SensorReading:
  zone_id: str
  temperature_f: float
  humidity: int
  occupied: bool


def build_thermostat_alerts(readings: list[SensorReading]) -> list[str]:
  alerts: list[str] = []

  for reading in readings:
    if reading.occupied and reading.temperature_f > 78:
      alerts.append(f"CRITICAL | {reading.zone_id} | HIGH_HEAT")
    if reading.temperature_f < 60:
      alerts.append(f"CRITICAL | {reading.zone_id} | FREEZE_RISK")
    if reading.humidity > 70:
      alerts.append(f"INFO | {reading.zone_id} | AIR_QUALITY_CHECK")

  severity_rank = {"CRITICAL": 0, "WARNING": 1, "INFO": 2}

  def alert_sort_key(alert: str) -> tuple[int, str, str]:
    severity, zone_id, reason = alert.split(" | ", 2)
    return (severity_rank[severity], zone_id, reason)

  return sorted(alerts, key=alert_sort_key)


def summarize_severity_mix(alerts: list[str]) -> str:
  counts = {"CRITICAL": 0, "WARNING": 0, "INFO": 0}

  for alert in alerts:
    severity = alert.split(" | ", 1)[0]
    if severity in counts:
      counts[severity] += 1

  return ";".join(
    f"{severity}={counts[severity]}" for severity in ("CRITICAL", "WARNING", "INFO")
  )


def print_alerts(alerts: list[str]) -> None:
  if not alerts:
    print("<no alerts>")
    return

  for alert in alerts:
    print(alert)


def main() -> None:
  readings = [
    SensorReading("LivingRoom", 81.5, 45, True),
    SensorReading("Basement", 57.0, 60, False),
    SensorReading("Lab", 74.0, 72, True),
    SensorReading("Office", 76.0, 48, True),
  ]

  alerts = build_thermostat_alerts(readings)
  print_alerts(alerts)
  print(f"Summary: {summarize_severity_mix(alerts)}")


if __name__ == "__main__":
  main()
```

### `alerting-policy.md`

```markdown
# Alerting Policy — Python

Use this file as the attached business context for the final Prompt Engineering demo.

## Output contract

- Return alert lines in the exact format `SEVERITY | zoneId | reason`.
- Allowed severities are `CRITICAL`, `WARNING`, and `INFO`.
- Keep the output deterministic: sort by severity rank (`CRITICAL`, then `WARNING`, then `INFO`),
  then by `zoneId`, then by `reason`.

## Alert rules

- Raise `HIGH_HEAT` as `CRITICAL` when `occupied == true` and `temperatureF > 78`.
- Raise `FREEZE_RISK` as `CRITICAL` when `temperatureF < 60`.
- Raise `AIR_QUALITY_CHECK` as `INFO` when `humidity > 70`.
- A single reading may emit more than one alert if more than one rule matches.
- If no rule matches, emit nothing for that reading.

## Summary contract

- `summarize_severity_mix` must return exactly `CRITICAL=n;WARNING=n;INFO=n`.
- Always include all three categories, even when a count is zero.
- Derive the counts from the existing alert strings. Do not duplicate the business rules there.

## Constraints

- Keep the file Python 3.11 and standard-library only.
- Do not change the sample data in `main()` during the final demo.
- Prefer readable code over clever code.
```

## Setup

| Setting | Value |
|---|---|
| **Surface** | Ask mode — do **not** change the chat surface during the demo |
| **What changes** | Only the **model picker** (Part B) and the **attached context** (Part C) |
| **Prep** | Copy both files from this page, open `sensor_alerts.py`, and have the model picker and token/usage meter visible. |

### How to read token usage

- In VS Code Chat, hover the response / open request details to see token counts, or watch your
  usage meter. Keep a whiteboard tally: **Model → rough tokens → quality note.**
- If exact tokens aren't visible in your build, use **response length + latency** as the proxy and
  say so. The teaching point survives either way.

---

## Part A — Establish the shared prompt (≈1 min)

Open `sensor_alerts.py`. This is the **one prompt** reused verbatim for every model:

```
Implement summarize_severity_mix(alerts: list[str]) -> str so it returns exactly CRITICAL=n;WARNING=n;INFO=n based on the existing alert strings. Keep parsing simple and deterministic. Do not change build_thermostat_alerts() or main().
```

Write it once, keep it on screen. **Do not edit it for the rest of Part B.**

---

## Part B — Same prompt, four models (≈5–6 min)

Run the identical prompt once per model. **Discard between runs** (don't accept) so every model sees
the same starting file. Fill the scoreboard after each run.

- **Run 1 — Auto:** set the picker to **Auto**, send the prompt.
- **Run 2 — Fast general model** (e.g. GPT-4.1 / a lightweight model): same prompt.
- **Run 3 — Reasoning model** (e.g. GPT-5.4): same prompt.
- **Run 4 — Different vendor** (e.g. Claude Sonnet): same prompt.

| Model | Correct contract? | Handles edge cases? | Over-engineered? | ~Tokens | Latency |
|---|---|---|---|---|---|
| Auto | | | | | |
| Fast model | | | | | |
| Reasoning model | | | | | |
| Other vendor | | | | | |

### Talking points

- **Same prompt ≠ same answer.** Naming, parsing strategy, and how strictly each model honors
  "do not change `main()`" will differ.
- **Reasoning models cost more tokens** and are slower, but tend to preserve constraints and edge
  cases better on hard prompts.
- **Auto** picks a model for you — note which behavior it landed closest to.
- A cheaper/faster model is frequently *good enough* for a well-specified, small task like this.

---

## Part C — Context makes the cheap model win (≈3–4 min)

Attach `alerting-policy.md`, switch **back to the fast / Auto model**, and run a context-aware prompt:

```
Using #alerting-policy.md, implement summarize_severity_mix(alerts: list[str]) -> str so it returns exactly CRITICAL=n;WARNING=n;INFO=n based on the existing alert strings. Keep parsing simple and deterministic. Do not change build_thermostat_alerts() or main().
```

**Talking point:** the policy file now carries the contract, so the **fast model produces what the
reasoning model produced in Part B — for fewer tokens.** Context is cheaper than reasoning.

**Follow-up — force the trade-off out loud:**
```
Explain the parsing approach you chose in two sentences and whether a simpler approach would pass the same contract.
```

---

## Part D — Let Auto decide, then verify (≈2 min)

Switch back to **Auto** and run one slightly harder ask on the same file:

```
Add a lightweight self-check in main() that prints "summary : PASS" when summarize_severity_mix matches the expected CRITICAL=2;WARNING=0;INFO=1 for the sample data, and "summary : FAIL" otherwise. Do not change the sample readings.
```

**Talking point:** **Auto is the sensible default** — reach for a named reasoning model only when the
task is genuinely hard or constraint-dense. Right-sizing the model is itself a prompting skill.

---

## Verify

```bash
python sensor_alerts.py
```

Expected:
```text
CRITICAL | Basement | FREEZE_RISK
CRITICAL | LivingRoom | HIGH_HEAT
INFO | Lab | AIR_QUALITY_CHECK
Summary: CRITICAL=2;WARNING=0;INFO=1
```

## Takeaway

1. **Model choice changes the answer, the depth, and the token bill** for the *exact same prompt*.
2. **Good context lets a cheaper model match an expensive one** — spend tokens on context, not on
   brute-force reasoning.
3. **Auto is the right default;** escalate to a reasoning model only when constraints get dense.
