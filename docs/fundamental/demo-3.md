---
title: Demo 3 — Writing effective prompts
layout: default
parent: Demos
grand_parent: Fundamental
nav_order: 3
permalink: /demos/demo-3.html
description: "Turn weak prompts into strong ones on a Java product catalog: goal, inputs, outputs, constraints, and @/# references."
---

# ✍️ Demo 3 — Writing Effective Prompts
{: .no_toc }

**Quick info**

- **Language:** Java
- **File:** `Catalog.java`
- **Run:** `javac Catalog.java && java Catalog`

You're building a small **product catalog utility** and learning to write strong prompts —
goal, inputs, outputs, constraints — plus `@` and `#` references and iterative refinement.
Five stubbed methods start **FAILing**; finish them with Copilot until you get `ALL PASS`.

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Setup

| Setting | Value |
|---|---|
| **Mode** | **Ask** (explain / discuss) and **Edit** / **Inline chat** (apply the change) |
| **Model** | GPT-5.4 |
| **File** | `Catalog.java` |

## Starter code — create `Catalog.java`

```java
// Demo 3 — Writing Effective Prompts
// Language: Java   |   Run: javac Catalog.java && java Catalog

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

public class Catalog {

    record Product(String name, double price, boolean inStock) {}

    static final List<Product> PRODUCTS = List.of(
        new Product("Keyboard", 45.0, true),
        new Product("Monitor", 210.0, false),
        new Product("Mouse", 25.0, true),
        new Product("Webcam", 45.0, true),
        new Product("Cable", 8.0, false)
    );

    // ---- Task 1: weak -> strong prompt --------------------------------
    // A NEW list of in-stock products, sorted by price ascending,
    // ties broken by product name A->Z. Do NOT mutate PRODUCTS.
    static List<Product> inStockSorted() {
        return new ArrayList<>(); // replace with Copilot's suggestion
    }

    // ---- Task 2: iterative refinement (add a constraint) --------------
    // In-stock products with price <= maxPrice, same sort as inStockSorted().
    static List<Product> inStockUnder(double maxPrice) {
        return new ArrayList<>(); // refine the prompt to add the price cap
    }

    // ---- Task 3: constraints & edge cases (Optional) ------------------
    // The single cheapest in-stock product, or Optional.empty() if none.
    static Optional<Product> cheapestInStock() {
        return Optional.empty(); // prompt Copilot to handle the empty case
    }

    // ---- Task 4: aggregate with a precision rule ---------------------
    // Total price of all in-stock products, rounded to 2 decimal places.
    static double inStockInventoryValue() {
        return 0.0; // ask for a sum with rounding
    }

    // ---- Task 5: exact output format --------------------------------
    // CSV of in-stock products in inStockSorted() order, each "name:price"
    // with price to exactly 2 decimals, comma-joined, no trailing comma.
    // Example shape: "Mouse:25.00,Keyboard:45.00,Webcam:45.00"
    static String inStockCsv() {
        return ""; // make the prompt specify the exact format
    }

    public static void main(String[] args) {
        List<String> names = new ArrayList<>();
        for (Product p : inStockSorted()) {
            names.add(p.name());
        }
        boolean checkSorted = names.equals(List.of("Mouse", "Keyboard", "Webcam"));

        List<Product> under = inStockUnder(30.0);
        boolean checkUnder = under.size() == 1 && under.get(0).name().equals("Mouse");

        boolean checkCheapest = cheapestInStock().map(Product::name).orElse("").equals("Mouse");

        boolean checkValue = inStockInventoryValue() == 115.0;

        boolean checkCsv = inStockCsv().equals("Mouse:25.00,Keyboard:45.00,Webcam:45.00");

        System.out.println("sorted     : " + (checkSorted ? "PASS" : "FAIL (Task 1: strong prompt)"));
        System.out.println("under_cap  : " + (checkUnder ? "PASS" : "FAIL (Task 2: refine with a cap)"));
        System.out.println("cheapest   : " + (checkCheapest ? "PASS" : "FAIL (Task 3: Optional/empty)"));
        System.out.println("value      : " + (checkValue ? "PASS" : "FAIL (Task 4: rounded sum)"));
        System.out.println("csv_format : " + (checkCsv ? "PASS" : "FAIL (Task 5: exact format)"));

        boolean all = checkSorted && checkUnder && checkCheapest && checkValue && checkCsv;
        System.out.println(all ? "ALL PASS" : "SOME FAILED - finish the tasks with Copilot, then re-run.");
    }
}
```

---

## Step 1 — Weak → strong prompt (`inStockSorted`)

**1a · Weak prompt (show the bad result first):**
```text
can you sort the products in this file and make the list a bit nicer for me
```

**1b · Strong prompt (goal + inputs + outputs + constraints + `#`):**
```text
Implement inStockSorted(): return a NEW list (do not mutate PRODUCTS) containing only products where inStock is true, sorted by price ascending, breaking ties on equal price by product name A→Z.
```

> **Anatomy of a strong prompt** — *Goal* (implement `inStockSorted`) · *Inputs* (`PRODUCTS`) ·
> *Output* (a new list) · *Constraints* (in-stock only, price asc, tie by name, don't mutate) ·
> *Context* (`#Catalog.java`).

## Step 2 — Iterative refinement (`inStockUnder`)

**2a · First pass (intentionally broad):**
```text
Implement inStockUnder(double maxPrice) to return in-stock products no more expensive than maxPrice.
```
**2b · Refine — pin the sort:**
```text
Sort the result by price ascending, then by name A→Z, and return a new list without mutating PRODUCTS.
```
**2c · Refine — confirm the boundary:**
```text
Make the cap inclusive: price <= maxPrice, not strictly less than.
```

## Step 3 — Naming edge cases (`cheapestInStock`)

**3a · Vague:**
```text
Get the cheapest in-stock product.
```
**3b · Strong (name every edge case):**
```text
Implement cheapestInStock() to return an Optional<Product> holding the lowest-priced in-stock product. Return Optional.empty() when nothing is in stock — do not throw and do not return null.
```

## Step 4 — Precision constraints (`inStockInventoryValue`)

**4a · Loose:**
```text
Sum the prices of in-stock products.
```
**4b · Precise:**
```text
Implement inStockInventoryValue() sum the price of every in-stock product and round the result to exactly 2 decimal places. Return the rounded double.
```

## Step 5 — Exact output format (`inStockCsv`)

**5a · Vague:**
```text
Export in-stock products as CSV.
```
**5b · Format-locked (give an example):**
```text
Implement inStockCsv() Using inStockSorted() order, join each product as "name:price" where price is formatted to exactly 2 decimals. Separate entries with commas and no trailing comma. It must produce exactly: Mouse:25.00,Keyboard:45.00,Webcam:45.00
```

## Step 6 — `@` references (Ask mode, no edits)

```text
@vscode how do I configure a task to compile and run a single Java file?
```
```text
@terminal what was the exact command that compiled and ran Catalog?
```

---

## Run

```bash
javac Catalog.java && java Catalog
```

Expected output:

```text
sorted     : PASS
under_cap  : PASS
cheapest   : PASS
value      : PASS
csv_format : PASS
ALL PASS
```

---

## Solution (completed methods)

<details markdown="block">
<summary>Click to reveal the five completed Java methods</summary>

```java
import java.util.Comparator;
import java.util.stream.Collectors;

static List<Product> inStockSorted() {
    return PRODUCTS.stream()
        .filter(Product::inStock)
        .sorted(Comparator.comparingDouble(Product::price)
                          .thenComparing(Product::name))
        .collect(Collectors.toList());
}

static List<Product> inStockUnder(double maxPrice) {
    return inStockSorted().stream()
        .filter(p -> p.price() <= maxPrice)
        .collect(Collectors.toList());
}

static Optional<Product> cheapestInStock() {
    return PRODUCTS.stream()
        .filter(Product::inStock)
        .min(Comparator.comparingDouble(Product::price)
                       .thenComparing(Product::name));
}

static double inStockInventoryValue() {
    double sum = PRODUCTS.stream()
        .filter(Product::inStock)
        .mapToDouble(Product::price)
        .sum();
    return Math.round(sum * 100.0) / 100.0;
}

static String inStockCsv() {
    return inStockSorted().stream()
        .map(p -> String.format("%s:%.2f", p.name(), p.price()))
        .collect(Collectors.joining(","));
}
```

</details>
