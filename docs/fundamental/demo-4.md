---
title: Demo 4 — Tips and techniques
layout: default
parent: Demos
grand_parent: Fundamental
nav_order: 4
permalink: /demos/demo-4.html
description: "Context, refactoring to LINQ, documentation, multilingual convert to Dart, /tests, and an MCP intro on a working C# inventory class."
---

# 🛠️ Demo 4 — Tips and Techniques
{: .no_toc }

**Quick info**

- **Language:** C# → converted to **Flutter/Dart**
- **File:** `Inventory.cs`

This class already works — run it first so the room sees a passing baseline, then use Copilot to
add context, refactor to LINQ, document, convert to another language, and generate tests.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Setup

| Setting | Value |
|---|---|
| **Modes shown** | **Edit** / **Inline chat** (refactor, docs, convert), **Ask** (explain), `/tests`, `#`/`@` context |
| **Model** | GPT-5.4 for everything |
| **File** | `Inventory.cs` (a small, working class — 4 methods, all PASS) |
| **Run** | Put `Inventory.cs` into a `dotnet new console` project as `Program.cs`, then `dotnet run` |

## Starter code — create `Inventory.cs`

```csharp
// Demo 4 — Tips and Techniques
// Language: C#   |   Run: put in a `dotnet new console` project as Program.cs, then `dotnet run`

using System;
using System.Collections.Generic;

class Item
{
    public string Sku { get; set; }
    public string Name { get; set; }
    public int OnHand { get; set; }
    public int ReorderAt { get; set; }

    public Item(string sku, string name, int onHand, int reorderAt)
    {
        Sku = sku;
        Name = name;
        OnHand = onHand;
        ReorderAt = reorderAt;
    }
}

class Inventory
{
    static readonly List<Item> Items = new List<Item>
    {
        new Item("KB-01", "Keyboard", 3, 5),
        new Item("MS-02", "Mouse", 12, 5),
        new Item("MN-03", "Monitor", 1, 2),
        new Item("CB-04", "Cable", 8, 4),
    };

    // True when the item is at or below its reorder threshold.
    static bool NeedsReorder(Item item)
    {
        return item.OnHand <= item.ReorderAt;
    }

    // Units to order to bring an item back up to its "par level" (twice the
    // reorder threshold). Returns 0 when the item does not need reordering.
    static int ReorderQuantity(Item item)
    {
        if (!NeedsReorder(item))
        {
            return 0;
        }
        int parLevel = item.ReorderAt * 2;
        return parLevel - item.OnHand;
    }

    // Return the list of item names that need reordering.
    static List<string> ReorderReport(List<Item> items)
    {
        var result = new List<string>();
        foreach (var item in items)
        {
            if (NeedsReorder(item))
            {
                result.Add(item.Name);
            }
        }
        return result;
    }

    // Total units to order across the whole catalog.
    static int TotalUnitsToOrder(List<Item> items)
    {
        int total = 0;
        foreach (var item in items)
        {
            total += ReorderQuantity(item);
        }
        return total;
    }

    static void Main()
    {
        bool check1 = NeedsReorder(Items[0]) && !NeedsReorder(Items[1]);

        var report = ReorderReport(Items);
        bool check2 = report.Count == 2 && report[0] == "Keyboard" && report[1] == "Monitor";

        // Keyboard: par 10 - onHand 3 = 7 ; Mouse is well stocked -> 0.
        bool check3 = ReorderQuantity(Items[0]) == 7 && ReorderQuantity(Items[1]) == 0;

        // Keyboard 7 + Monitor 3 = 10 (Mouse and Cable need nothing).
        bool check4 = TotalUnitsToOrder(Items) == 10;

        Console.WriteLine($"NeedsReorder    : {(check1 ? "PASS" : "FAIL")}");
        Console.WriteLine($"ReorderReport   : {(check2 ? "PASS" : "FAIL")}");
        Console.WriteLine($"ReorderQuantity : {(check3 ? "PASS" : "FAIL")}");
        Console.WriteLine($"TotalUnitsOrder : {(check4 ? "PASS" : "FAIL")}");
        Console.WriteLine((check1 && check2 && check3 && check4) ? "ALL PASS" : "SOME FAILED");
    }
}
```

---

## Step 1 — Commands & context

- Type `/` in Chat — the **command menu** appears (`/tests`, `/explain`, `/fix`, …).
- Type `#` — the **context picker** appears. Attach different scopes:
```text
Using #Inventory.cs, list every method and its one-line purpose.
```
```text
Explain #NeedsReorder and when it returns true.
```
(Select `ReorderQuantity` first, then:) `Explain #selection like I'm new to the codebase.`

## Step 2 — Documentation (Inline chat)

Select the whole `ReorderQuantity` method → press **Ctrl+I** (or Edit mode):
```text
Add an XML documentation comment above ReorderQuantity: a <summary> plus <param> and <returns>, explaining the par-level rule.
```

## Step 3 — Refactoring (Edit mode)

**3a · `ReorderReport` → LINQ:**
```text
Refactor ReorderReport to use LINQ (Where + Select). Keep the behavior and return value identical.
```
**3b · `TotalUnitsToOrder` → LINQ:**
```text
Refactor TotalUnitsToOrder to a single LINQ Sum(...) over ReorderQuantity. Do not change the result.
```
Re-run (`dotnet run`) — still `ALL PASS`. **Refactoring changes shape, not behavior.**

## Step 4 — Multilingual: convert to Flutter/Dart

```text
Convert this code to Flutter/Dart code named inventory.dart. Keep the same class shape, method names, and logic for all four methods, and include a main() that prints the same checks.
```
Save the output as `inventory.dart`.

## Step 5 — Tests *(intro only)*

Select `ReorderQuantity` → Chat → run:
```text
/tests
```

---

## Run

```bash
# C# (inside a dotnet console project, Inventory.cs as Program.cs):
dotnet run

# after the convert step:
dart run inventory.dart
```

Expected C# output:

```text
NeedsReorder    : PASS
ReorderReport   : PASS
ReorderQuantity : PASS
TotalUnitsOrder : PASS
ALL PASS
```

---

## Solution (LINQ refactors + Dart conversion)

<details markdown="block">
<summary>Click to reveal the LINQ refactors</summary>

```csharp
using System.Linq;

static List<string> ReorderReport(List<Item> items)
{
    return items.Where(NeedsReorder).Select(i => i.Name).ToList();
}

static int TotalUnitsToOrder(List<Item> items)
{
    return items.Sum(ReorderQuantity);
}
```

</details>

<details markdown="block">
<summary>Click to reveal the converted <code>inventory.dart</code></summary>

```dart
// inventory.dart  |  Run: dart run inventory.dart

class Item {
  final String sku;
  final String name;
  final int onHand;
  final int reorderAt;
  Item(this.sku, this.name, this.onHand, this.reorderAt);
}

final List<Item> items = [
  Item("KB-01", "Keyboard", 3, 5),
  Item("MS-02", "Mouse", 12, 5),
  Item("MN-03", "Monitor", 1, 2),
  Item("CB-04", "Cable", 8, 4),
];

bool needsReorder(Item item) => item.onHand <= item.reorderAt;

int reorderQuantity(Item item) {
  if (!needsReorder(item)) return 0;
  final parLevel = item.reorderAt * 2;
  return parLevel - item.onHand;
}

List<String> reorderReport(List<Item> items) =>
    items.where(needsReorder).map((i) => i.name).toList();

int totalUnitsToOrder(List<Item> items) =>
    items.fold(0, (sum, i) => sum + reorderQuantity(i));

void main() {
  final check1 = needsReorder(items[0]) && !needsReorder(items[1]);

  final report = reorderReport(items);
  final check2 = report.length == 2 && report[0] == "Keyboard" && report[1] == "Monitor";

  final check3 = reorderQuantity(items[0]) == 7 && reorderQuantity(items[1]) == 0;

  final check4 = totalUnitsToOrder(items) == 10;

  print("NeedsReorder    : ${check1 ? "PASS" : "FAIL"}");
  print("ReorderReport   : ${check2 ? "PASS" : "FAIL"}");
  print("ReorderQuantity : ${check3 ? "PASS" : "FAIL"}");
  print("TotalUnitsOrder : ${check4 ? "PASS" : "FAIL"}");
  print((check1 && check2 && check3 && check4) ? "ALL PASS" : "SOME FAILED");
}
```

</details>
