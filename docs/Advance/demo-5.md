---
title: Demo 5 — Legacy Modernization
layout: default
parent: Demos
grand_parent: Advanced
nav_order: 5
permalink: /advance/demos/demo-5.html
description: "Three legacy-modernization runbooks: generate tests from legacy Python rules, port a parser to C# with parity evidence, and phase-modernize a .NET worker with the Upgrade agent."
---

# 🛠️ Demo 5 — Legacy Modernization
{: .no_toc }

**Deck checkpoint:** Slide 34 / Demo 5
**Duration:** ~8–10 minutes per part
**Languages / surfaces:** Python · C# · .NET · GitHub Copilot Upgrade agent

> **Advanced focus:** Modernize legacy code with control — start with behavior
> discovery and invariants, demand parity evidence before code, and modernize in
> reviewable phases instead of a blind rewrite. Each part below is a standalone
> runbook; run one or all three.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# Part A — Legacy Test Generation

## Files

`thermostat_rules.py` · `test-matrix-template.md`

### `thermostat_rules.py`

```python
from dataclasses import dataclass
from enum import Enum


class FanMode(str, Enum):
    OFF = "off"
    LOW = "low"
    MEDIUM = "medium"
    HIGH = "high"


@dataclass(frozen=True)
class SensorSnapshot:
    indoor_temp_f: float
    target_temp_f: float
    humidity_percent: float
    window_open: bool
    occupancy_detected: bool


def determine_fan_mode(snapshot: SensorSnapshot) -> FanMode:
    if snapshot.window_open:
        return FanMode.OFF

    delta = snapshot.target_temp_f - snapshot.indoor_temp_f

    if not snapshot.occupancy_detected and abs(delta) < 2.0:
        return FanMode.LOW

    if delta > 5.0:
        return FanMode.HIGH

    if delta > 2.0:
        return FanMode.MEDIUM

    if delta < -4.0 or snapshot.humidity_percent > 68.0:
        return FanMode.HIGH

    if delta < -1.0:
        return FanMode.MEDIUM

    return FanMode.LOW


def should_schedule_filter_change(
    runtime_hours: int,
    days_since_change: int,
    airflow_restricted: bool,
) -> bool:
    if airflow_restricted:
        return True

    if runtime_hours >= 2200:
        return True

    if days_since_change >= 180 and runtime_hours >= 1400:
        return True

    return days_since_change >= 365


def build_escalation_reason(
    compressor_short_cycling: bool,
    sensor_mismatch: bool,
    repeated_humidity_spike: bool,
) -> str:
    if compressor_short_cycling and sensor_mismatch:
        return "inspect-compressor-and-calibrate-sensors"

    if compressor_short_cycling:
        return "inspect-compressor"

    if sensor_mismatch:
        return "calibrate-sensors"

    if repeated_humidity_spike:
        return "check-airflow-and-drain"

    return "monitor-next-cycle"
```

### `test-matrix-template.md`

```markdown
# Demo 1 - Test Matrix Template

Use this file to capture the branch map Copilot produces before generating any
tests.

| Function | Branch or edge case | Expected result | Risk if missing |
| --- | --- | --- | --- |
| determine_fan_mode | window open | FanMode.OFF | Equipment runs when it should stop |
| determine_fan_mode | add more thresholds here | | |
| should_schedule_filter_change | airflow restricted | True | Maintenance signal missed |
| build_escalation_reason | combined compressor + sensor issue | inspect-compressor-and-calibrate-sensors | Wrong field action |

Presenter note: keep at least one threshold case, one negative case, and one
default-path case in the final matrix.
```

## Goal

Use Copilot to turn a legacy Python rules file into a test-first improvement loop
without editing production logic first.

## Live flow

### Part A.1 — Map the risk before generating tests

Open `thermostat_rules.py` and ask Copilot Chat:

```text
Read this legacy Python file and do not write code yet. List the behavior
branches, threshold boundaries, and failure-path tests that should exist for
each function. Group the answer as a test matrix I can paste into Markdown.
```

Paste the result into `test-matrix-template.md` or a scratch note.

### Part A.2 — Generate the first test harness

Follow with:

```text
Now create test_thermostat_rules.py using Python's built-in unittest module.
Keep the test names readable and cover every branch you just listed. Do not
change thermostat_rules.py.
```

### Part A.3 — Run one validation loop

Run the generated test file.

```bash
python -m unittest -q test_thermostat_rules.py
```

If a test fails, use a narrow repair prompt:

```text
One assertion failed. Explain whether the test or the production logic is wrong,
then propose the smallest safe fix and the exact reason for it.
```

### Part A.4 — Add a thin integration layer

Once the unit tests are stable, ask Copilot Chat:

```text
Keep unittest for unit tests. Add a tiny Flask app that exposes the thermostat
rules through JSON endpoints, then create integration tests for that app using
Flask's test client. Do not change thermostat_rules.py.
```

Run the full demo suite.

```bash
python -m unittest -q
```

If Flask is not installed in the environment, install it first.

```bash
pip install flask
```

## What to point out

- Good advanced usage starts with behavior discovery, not code generation.
- The first deliverable is a branch map and edge-case matrix.
- Tests are the proof artifact you share with the client, not the chat alone.
- The human decides whether to change production logic after the tests exist.
- Unit tests prove rule correctness in isolation; integration tests prove the public boundary.

## Optional extension

Ask Copilot for two more negative tests focused on suspicious thresholds or
default paths.

If you want a browser-based end-to-end demo later, add a small HTML form on top
of the Flask app and use Selenium only for that UI layer.

For the lightweight UI demo in this folder, run the Flask app and the Selenium
smoke test separately from the core unit and integration suite.

```bash
python thermostat_api.py
python -m unittest -q thermostat_ui_selenium_demo.py
```

If Selenium is missing in another environment, install it first.

```bash
pip install selenium
```

---

# Part B — Migration Parity

## Files

`legacy_schedule_parser.py` · `migration-cases.md`

### `legacy_schedule_parser.py`

```python
from dataclasses import dataclass


@dataclass(frozen=True)
class ScheduleEntry:
    hour: int
    minute: int
    setpoint_f: int


def parse_day_program(text: str) -> list[ScheduleEntry]:
    entries: list[ScheduleEntry] = []

    for raw_token in text.split(";"):
        token = raw_token.strip()
        if not token:
            continue

        if "=" not in token:
            raise ValueError("missing setpoint separator")

        time_text, setpoint_text = [part.strip() for part in token.split("=", 1)]

        if len(time_text) != 5 or time_text[2] != ":":
            raise ValueError("time must be HH:MM")

        hour = int(time_text[:2])
        minute = int(time_text[3:5])
        setpoint_f = int(setpoint_text)

        if hour < 0 or hour > 23 or minute < 0 or minute > 59:
            raise ValueError("time value out of range")

        if setpoint_f < 45 or setpoint_f > 90:
            raise ValueError("setpoint out of range")

        if entries:
            previous = entries[-1]
            if hour < previous.hour or (
                hour == previous.hour and minute <= previous.minute
            ):
                raise ValueError("entries must be strictly increasing")

        entries.append(ScheduleEntry(hour=hour, minute=minute, setpoint_f=setpoint_f))

    if not entries:
        raise ValueError("program cannot be empty")

    return entries


def normalize_day_program(entries: list[ScheduleEntry]) -> str:
    if not entries:
        raise ValueError("program cannot be empty")

    return ";".join(
        f"{entry.hour:02d}:{entry.minute:02d}={entry.setpoint_f}"
        for entry in entries
    )
```

### `migration-cases.md`

```markdown
# Demo 2 - Migration Cases

Use these cases to judge whether the migrated parser preserved behavior.

| Case | Input | Expected outcome |
| --- | --- | --- |
| Happy path | `06:30=68;22:00=62` | Normalized output stays `06:30=68;22:00=62` |
| Whitespace | ` 06:30=68 ; 22:00=62 ` | Whitespace ignored, same normalized output |
| Empty token | `06:30=68;;22:00=62` | Empty token ignored |
| Decreasing time | `22:00=62;06:30=68` | Error: `entries must be strictly increasing` |
| Bad setpoint | `06:30=101` | Error: `setpoint out of range` |

Presenter note: ask Copilot to map each case to a target-language test or
parity check after the port is generated.
```

## Goal

Show how Copilot can migrate a focused Python parser to another language while
preserving behavior and surfacing ambiguity early.

## Live flow

### Part B.1 — Demand invariants first

Open `legacy_schedule_parser.py` and `migration-cases.md`, then ask:

```text
Read this Python parser and the parity cases. Before you generate code, list the
behavior invariants, exact error conditions, and any ambiguous rules that must
stay consistent in the migrated version.
```

### Part B.2 — Port with parity constraints

Follow with:

```text
Now port this parser to C#. Keep the same parsing rules, the same normalized
output format, and the same error messages wherever practical. After the code,
return a short parity checklist mapping each migration case to the expected
result.
```

### Part B.3 — Review the migration result

Ask one final review question:

```text
What parts of this port are still risky, and which parity cases should be run
first before we approve the migration?
```

## What to point out

- Migration quality depends on captured invariants, not just translated syntax.
- One parser or boundary at a time is easier to validate than broad rewrites.
- The parity checklist is the client-ready artifact, not just the target code.

## Optional extension

Ask Copilot to generate unit-test stubs for the target language using the same
cases from `migration-cases.md`.

---

# Part C — Modernization with Observability

## Files

`DeviceSyncWorker.cs` · `DeviceSyncWorker.csproj` · `modernization-acceptance.md`

### `DeviceSyncWorker.cs`

```csharp
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace DeviceSyncWorkerDemo;

public sealed record DeviceRecord(
    string DeviceId,
    bool Online,
    bool NeedsConfigPush,
    int BatteryPercent,
    int RetryCount
);

public sealed record SyncResult(
    int Attempted,
    int Updated,
    int Skipped,
    int Failed
);

public sealed record DeviceSyncSettings(
    TimeSpan PollInterval,
    int MinimumBatteryPercent,
    int MaxRetryCount,
    bool DryRun
);

public interface IDeviceInventoryClient
{
    Task<IReadOnlyList<DeviceRecord>> GetDevicesAsync(CancellationToken cancellationToken);
}

public interface IConfigurationPublisher
{
    Task PublishAsync(DeviceRecord device, CancellationToken cancellationToken);
}

public interface ISyncMetrics
{
    void TrackBatch(SyncResult result);
}

public sealed class DeviceSyncWorker : BackgroundService
{
    private readonly ILogger<DeviceSyncWorker> _logger;
    private readonly IDeviceInventoryClient _inventoryClient;
    private readonly IConfigurationPublisher _configurationPublisher;
    private readonly ISyncMetrics _metrics;
    private readonly DeviceSyncSettings _settings;

    public DeviceSyncWorker(
        ILogger<DeviceSyncWorker> logger,
        IDeviceInventoryClient inventoryClient,
        IConfigurationPublisher configurationPublisher,
        ISyncMetrics metrics,
        DeviceSyncSettings settings
    )
    {
        _logger = logger;
        _inventoryClient = inventoryClient;
        _configurationPublisher = configurationPublisher;
        _metrics = metrics;
        _settings = settings;
    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)
    {
        using var timer = new PeriodicTimer(_settings.PollInterval);

        while (await timer.WaitForNextTickAsync(stoppingToken))
        {
            var devices = await _inventoryClient.GetDevicesAsync(stoppingToken);
            var attempted = 0;
            var updated = 0;
            var skipped = 0;
            var failed = 0;

            foreach (var device in devices)
            {
                if (!device.Online)
                {
                    _logger.LogInformation("skip {DeviceId}: offline", device.DeviceId);
                    skipped++;
                    continue;
                }

                if (device.BatteryPercent < _settings.MinimumBatteryPercent)
                {
                    _logger.LogInformation("skip {DeviceId}: low battery", device.DeviceId);
                    skipped++;
                    continue;
                }

                if (!device.NeedsConfigPush)
                {
                    _logger.LogInformation("skip {DeviceId}: already current", device.DeviceId);
                    skipped++;
                    continue;
                }

                attempted++;

                if (device.RetryCount > _settings.MaxRetryCount)
                {
                    _logger.LogWarning(
                        "fail {DeviceId}: retry budget exhausted",
                        device.DeviceId
                    );
                    failed++;
                    continue;
                }

                if (_settings.DryRun)
                {
                    _logger.LogInformation(
                        "dry-run {DeviceId}: would push configuration",
                        device.DeviceId
                    );
                    updated++;
                    continue;
                }

                await _configurationPublisher.PublishAsync(device, stoppingToken);
                _logger.LogInformation(
                    "push {DeviceId}: configuration sent",
                    device.DeviceId
                );
                updated++;
            }

            var summary = new SyncResult(
                Attempted: attempted,
                Updated: updated,
                Skipped: skipped,
                Failed: failed
            );

            _metrics.TrackBatch(summary);
            _logger.LogInformation(
                "sync_summary {SummaryJson}",
                JsonConvert.SerializeObject(summary)
            );
        }
    }
}
```

### `DeviceSyncWorker.csproj`

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net9.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
  </PropertyGroup>
  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Newtonsoft.Json" Version="13.0.3" />
  </ItemGroup>
</Project>
```

### `modernization-acceptance.md`

```markdown
# Demo 3 - Modernization Acceptance

Use this checklist to keep the modernization demo grounded.

## Phase-1 acceptance criteria

- Preserve the same skip reasons: `offline`, `low battery`, `already current`.
- Preserve the same failure reason: `retry budget exhausted`.
- Preserve the same summary fields: attempted, updated, skipped, failed.
- Keep the sample project buildable with `dotnet build DeviceSyncWorker.csproj`.
- Keep the `BackgroundService` loop and cancellation flow intact.
- Replace `Newtonsoft.Json` only if the serialized summary shape stays intact.
- Extract at least one pure helper and one reporting seam.
- Make logging and metrics easier to replace later without changing business
  decisions in phase 1.

## Questions for the client review

1. Which part of the workflow changes most often today?
2. Which decisions should become independently testable first?
3. What operational signal is missing when a sync batch goes wrong?
```

## Goal

Use Copilot to modernize a monolithic .NET workflow in phases by introducing
seams for logging, metrics, and retry policy before attempting deeper changes.
Use the GitHub Copilot upgrade extension at the start so the demo begins with a
real modernization workflow rather than a generic refactor prompt.

## Tooling in the demo

- Search in VS Code Extensions for **GitHub Copilot Upgrade** and install it.
  Then you will see `@upgrade` in Copilot Chat.
- Open Copilot Chat and select the **Upgrade** agent, or use the `@upgrade`
	participant.
- This extension is designed for upgrade and migration tasks such as framework
	upgrades and supported package migrations.

## Live flow

### Part C.1 — Start with the Upgrade agent

Open `DeviceSyncWorker.cs` and ask:

```text
@upgrade Inspect this .NET worker project. Identify the safest phase-1
modernization tasks that do not change runtime behavior, and prioritize any
supported framework or package migrations we should review first.
```

Expected talking point: the agent should identify reviewable work instead of
jumping straight to a rewrite. Use that to explain why modernization is a
sequence of controlled tasks.

### Part C.2 — Drive one supported upgrade task

Use the supported migration path in this sample:

```text
@upgrade Replace Newtonsoft.Json with System.Text.Json where it is safe in this
project. Explain the code changes before applying them.
```

### Part C.3 — Implement only phase 1 refactoring

Follow with:

```text
Implement only phase 1. Extract pure helper functions and one reporting seam,
keep the same summary fields, and preserve the current skip and failure reasons.
```

### Part C.4 — Verify the project still builds

After applying any phase-1 edits, build the sample project.

```bash
dotnet build DeviceSyncWorker.csproj
```

### Part C.5 — Check operational evidence

Close with:

```text
Summarize what observability improved, what behavior stayed the same, and what
should wait for phase 2.
```

## What to point out

- Advanced modernization is controlled decomposition, not a blind rewrite.
- The Upgrade agent is useful for discovering supported migration tasks and
	sequencing them into reviewable work.
- Phase 1 should preserve business behavior while improving change safety.
- The client-ready artifact is the phased plan plus the acceptance checklist.

## Optional extension

Ask Copilot for a phase-2 roadmap that adds dependency injection and retry
policy tests without changing the public behavior.
