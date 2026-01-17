# Open Cheese Recipe Specification (OCRS) v1.0

## 1. Purpose

The Open Cheese Recipe Specification (OCRS) defines a **vendor-neutral, human-readable, and machine-readable** format for documenting cheese recipes.

Its goals are to:

* Standardize how cheese recipes are written and shared
* Support recipes from books, videos, and experimentation
* Preserve traditional cheesemaking knowledge
* Enable interoperability between tools, apps, and communities

OCRS is **not** tied to any application, country, or commercial product.

---

## 2. Design Principles

1. **Human-first** – A cheesemaker should understand the recipe without software
2. **Machine-readable** – Structured enough for validation, automation, and analysis
3. **Flexible** – Supports imprecise, traditional, and scientific recipes
4. **Backward-compatible** – New versions only add optional fields
5. **Minimal required fields** – Most fields are optional by design

---

## 3. Specification Versioning

Every OCRS document MUST declare a spec version:

```yaml
spec: OCRS/1.0
```

Future versions will follow semantic versioning:

* Major: breaking changes (avoided)
* Minor: additive features
* Patch: clarifications

---

## 4. Core Entities Overview

An OCRS recipe consists of the following top-level sections:

* `recipe` – Identity and context
* `ingredients` – Materials used
* `steps` – Process instructions
* `aging` (optional) – Ripening and storage
* `finalProduct` (optional) – Expected outcome

---

## 5. Recipe Metadata

```yaml
recipe:
  name: string (required)
  style: string (optional)
  milkType: string (optional)
  origin: string (optional)
  difficulty: BEGINNER | INTERMEDIATE | ADVANCED (optional)
  yield:
    amount: number
    unit: string
  batchSize:
    milkVolume: number
    unit: string
  source:
    type: BOOK | VIDEO | PERSONAL | OTHER
    reference: string
```

Notes:

* `style` refers to cheese family (e.g., FRESH, SEMI_HARD)
* `source.reference` may include URLs, book names, or timestamps

---

## 6. Ingredients

Ingredients MUST be listed separately from steps.

```yaml
ingredients:
  - name: string (required)
    type: PRIMARY | CULTURE | COAGULANT | ADDITIVE | SEASONING | OTHER
    amount: number
    unit: string
    preparation: string (optional)
```

Guidelines:

* Amounts SHOULD be numeric
* Units SHOULD be standardized where possible
* Preparation notes are free-text

---

## 7. Steps (Core Section)

Steps define the cheesemaking process. Each step MUST be atomic and ordered.

```yaml
steps:
  - stepNumber: number (required)
    title: string (required)
    category: string (optional)
    instructions: string (required)

    temperature:
      target: number
      min: number
      max: number
      unit: C | F

    ph:
      target: number

    duration:
      type: FIXED | UNTIL_CONDITION
      value: number
      unit: minutes | hours | days
      condition: string

    validation:
      expectedResult: string

    environment:
      roomTemperature: number
      humidity: string

    notes: string
```

Rules:

* Either `duration.value` or `duration.condition` SHOULD be present
* Temperature ranges are preferred over exact values
* Validation text is strongly encouraged

---

## 8. Aging & Storage (Optional)

```yaml
aging:
  required: boolean
  duration:
    min: number
    max: number
    unit: days | months
  temperature:
    value: number
    unit: C
  humidity: string
```

---

## 9. Final Product Description (Optional)

```yaml
finalProduct:
  texture: string
  flavor: string
  aroma: string
  moisture: HIGH | MEDIUM | LOW
  commonIssues:
    - string
```

---

## 10. Complete Example

```yaml
spec: OCRS/1.0

recipe:
  name: "Traditional Feta Cheese"
  style: FRESH
  milkType: SHEEP
  origin: Greece
  difficulty: INTERMEDIATE
  batchSize:
    milkVolume: 10
    unit: liters

ingredients:
  - name: Milk
    type: PRIMARY
    amount: 10
    unit: liters

  - name: Starter Culture
    type: CULTURE
    amount: 0.5
    unit: tsp

steps:
  - stepNumber: 1
    title: Heat milk
    instructions: Slowly heat milk while stirring gently.
    temperature:
      target: 32
      unit: C
    duration:
      type: FIXED
      value: 30
      unit: minutes
    validation:
      expectedResult: Milk feels warm but not hot.

  - stepNumber: 2
    title: Add rennet
    instructions: Add diluted rennet and stir gently.
    duration:
      type: UNTIL_CONDITION
      condition: Clean break achieved

finalProduct:
  texture: Crumbly
  flavor: Salty and tangy
```

---

## 11. Compliance

An OCRS-compliant recipe MUST:

* Declare a spec version
* Include `recipe.name`
* Include at least one step

All other fields are optional.

---

## 12. License

This specification is released under **Creative Commons Attribution 4.0 (CC-BY 4.0)**.

Anyone may use, extend, and implement this specification with attribution.

---

## 13. Governance

OCRS is community-maintained. Changes are proposed via public discussion and accepted through versioned releases.

The maintainer acts as steward, not owner, of the specification.
