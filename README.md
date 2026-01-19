# Open Cheese Recipe Specification (OCRS) v1.0

## 1. Purpose

The Open Cheese Recipe Specification (OCRS) defines a **vendor-neutral, human-readable, and machine-readable** format for documenting cheese recipes.

Its goals are to:

* Standardize how cheese recipes are written and shared
* Support recipes from books, videos, and experimentation
* Preserve traditional cheesemaking knowledge
* Enable interoperability between tools, apps, and communities

OCRS is **not** tied to any application, country, or commercial product.

### 1.1 Comprehensive Features

OCRS v1.0 includes comprehensive support for:
- Core recipe information (name, style, milk type, difficulty)
- Detailed ingredient specification with types and preparation
- Step-by-step instructions with temperature, pH, duration, and validation
- Aging and ripening requirements
- Final product characteristics
- Equipment requirements
- Cost and economics tracking
- Safety and allergen information
- Media attachments (photos/videos)
- Recipe variations and substitutions
- Scaling guidance
- Enhanced validation with visual, tactile, and measurement checks
- Multi-language support framework

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

* Major: breaking changes (avoided whenever possible)
* Minor: additive features (backward compatible)
* Patch: clarifications and fixes

---

## 4. Core Entities Overview

An OCRS recipe consists of the following top-level sections:

**Core Sections (required or strongly recommended):**
* `recipe` – Identity and context
* `ingredients` – Materials used
* `steps` – Process instructions

**Optional Sections:**
* `aging` – Ripening and storage
* `finalProduct` – Expected outcome characteristics
* `equipment` – Required and optional tools
* `cost` – Economic information
* `safety` – Allergens, warnings, and shelf life
* `media` – Photos and videos
* `variations` – Recipe alternatives and substitutions
* `scaling` – Guidelines for adjusting batch sizes
* `metadata` – Multi-language support and additional info

---

## 5. Recipe Metadata

```yaml
recipe:
  name: string (required)
  style: FRESH | SOFT | SEMI_SOFT | SEMI_HARD | HARD | BLUE | PASTA_FILATA | WASHED_RIND | OTHER (optional)
  milkType: COW | GOAT | SHEEP | BUFFALO | CAMEL | YAK | MIXED | OTHER (optional)
  origin: string (optional)
  difficulty: BEGINNER | INTERMEDIATE | ADVANCED (optional)
  yield:
    amount: number
    unit: string  # kg, lb, pieces, wheels, blocks
  batchSize:
    milkVolume: number
    unit: string  # liters, gallons, ml
  source:
    type: BOOK | VIDEO | WEBSITE | PERSONAL | COMMUNITY | OTHER
    reference: string
    author: string (optional)
    date: string (optional)  # ISO 8601 date
  prepTime: number (optional)  # minutes
  totalTime: number (optional)  # minutes
```

**Notes:**

* `style` refers to cheese family using standardized categories
* `milkType` uses animal source enums (use MIXED for blends)
* `source.reference` may include URLs, book titles with page numbers, or video timestamps
* Times are in minutes for consistency

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
  appearance: string (optional)
  color: string (optional)
  weight: number (optional)  # Expected final weight
  weightUnit: string (optional)  # kg, lb, g
  commonIssues:
    - issue: string
      cause: string (optional)
      remedy: string (optional)
```

**Notes:**

* `appearance` - Visual description of finished cheese
* `color` - Expected color (e.g., "pale yellow", "white")
* `weight` - Expected final weight after aging/moisture loss
* `commonIssues` - Structured with issue, cause, and remedy for troubleshooting

---

## 10. Equipment (Optional)

Equipment section lists tools and materials needed for the recipe.

```yaml
equipment:
  required:
    - name: string
      notes: string (optional)
  optional:
    - name: string
      notes: string (optional)
  specialEquipment:
    - name: string
      purpose: string (optional)
      alternatives: string (optional)
```

**Example:**

```yaml
equipment:
  required:
    - name: "Large pot (8-10 quarts)"
      notes: "Stainless steel or enamel-coated"
    - name: "Thermometer"
      notes: "Digital, range 0-100°C (32-212°F)"
    - name: "Cheesecloth or butter muslin"
    - name: "Long knife"
      notes: "For cutting curds"
  optional:
    - name: "pH meter"
      notes: "For precise acidity measurement"
  specialEquipment:
    - name: "Cheese press"
      purpose: "For pressed cheeses only"
      alternatives: "Weighted plate can be substituted"
```

**Guidelines:**

* List equipment in order of use
* Specify quantities, sizes, or capacities where relevant
* Include alternatives for specialized equipment
* Notes can include material specifications (stainless steel, food-grade plastic)

---

## 11. Cost & Economics (Optional)

Cost section provides economic information for recipe evaluation.

```yaml
cost:
  estimatedTotal: number
  currency: string  # ISO 4217 code (USD, EUR, GBP, etc.)
  breakdown:
    - item: string
      amount: number
      unit: string (optional)  # per liter, per batch, etc.
      notes: string (optional)
  profitability:
    estimatedSellingPrice: number (optional)
    profitMargin: number (optional)  # percentage
    costPerUnit: number (optional)  # cost per kg, lb, piece
```

**Example:**

```yaml
cost:
  estimatedTotal: 25.50
  currency: USD
  breakdown:
    - item: "Whole milk"
      amount: 15.00
      unit: "per 10 liters"
    - item: "Mesophilic culture"
      amount: 5.00
    - item: "Liquid rennet"
      amount: 3.50
    - item: "Salt"
      amount: 2.00
  profitability:
    estimatedSellingPrice: 45.00
    profitMargin: 76
    costPerUnit: 17.00  # per kg
```

**Guidelines:**

* All amounts in the specified currency
* Include only ingredient costs (not equipment)
* Labor costs are optional and implementation-specific
* Profitability section is for commercial producers

---

## 12. Safety & Allergens (Optional)

Safety section documents allergens, warnings, and storage requirements.

```yaml
safety:
  allergens:
    - MILK | LACTOSE | NUTS | SOY | EGGS | GLUTEN | OTHER
  warnings:
    - string
  shelfLife:
    fresh: string  # "7 days refrigerated"
    aged: string (optional)  # "6 months in brine"
  storageInstructions: string
  foodSafetyNotes: string (optional)
```

**Example:**

```yaml
safety:
  allergens:
    - MILK
    - LACTOSE
  warnings:
    - "Contains unpasteurized milk - not suitable for pregnant women, infants, or immunocompromised individuals"
    - "Must be refrigerated at all times"
  shelfLife:
    fresh: "7 days refrigerated (4°C)"
    aged: "6 months when stored in brine"
  storageInstructions: "Store in airtight container in refrigerator. For aged feta, keep submerged in brine."
  foodSafetyNotes: "Ensure all equipment is properly sanitized. Do not consume if mold appears (other than intentional surface mold)."
```

**Guidelines:**

* List all major allergens using standardized values
* Include regulatory warnings based on your region
* Shelf life should specify storage conditions
* Food safety notes for handling and sanitation

---

## 13. Media Attachments (Optional)

Media section links photos, videos, and other visual aids.

```yaml
media:
  photos:
    - url: string
      caption: string (optional)
      stepNumber: number (optional)  # Links to specific step
      stage: string (optional)  # "before", "during", "after"
  videos:
    - url: string
      title: string (optional)
      timestamp: string (optional)  # "12:34" for relevant moment
      stepNumber: number (optional)
      platform: YOUTUBE | VIMEO | OTHER (optional)
  documents:
    - url: string
      title: string
      type: string (optional)  # "PDF", "article", "blog post"
```

**Example:**

```yaml
media:
  photos:
    - url: "https://example.com/images/clean-break.jpg"
      caption: "Clean break test - curd should crack cleanly"
      stepNumber: 3
      stage: "during"
    - url: "https://example.com/images/final-feta.jpg"
      caption: "Finished feta after 2 weeks in brine"
      stage: "after"
  videos:
    - url: "https://youtube.com/watch?v=abc123"
      title: "Traditional Feta Making Process"
      timestamp: "12:34"
      stepNumber: 5
      platform: YOUTUBE
```

**Guidelines:**

* Use absolute URLs for all media
* Link photos to specific steps when relevant
* Video timestamps point to relevant moments in longer videos
* Caption all photos for accessibility

---

## 14. Recipe Variations (Optional)

Variations section documents alternative versions of the recipe.

```yaml
variations:
  - name: string
    description: string
    substitutions:
      - original: string
        replacement: string
        ratio: number (optional)  # scaling ratio
        notes: string (optional)
    adjustments:
      - field: string  # "temperature", "duration", "amount"
        value: string | number
        notes: string (optional)
    tags: [string] (optional)
```

**Example:**

```yaml
variations:
  - name: "Vegan Paneer"
    description: "Plant-based version using soy milk"
    substitutions:
      - original: "Cow milk"
        replacement: "Soy milk (organic, full-fat)"
        ratio: 1.0
        notes: "Must be high-protein soy milk"
      - original: "Lemon juice"
        replacement: "Apple cider vinegar or calcium sulfate"
        notes: "Calcium sulfate gives firmer texture"
    adjustments:
      - field: "coagulation time"
        value: "Reduce by 5 minutes"
    tags: ["vegan", "dairy-free"]

  - name: "Low-Salt Feta"
    description: "Reduced sodium version for dietary restrictions"
    adjustments:
      - field: "salt amount"
        value: 0.5
        notes: "Use half the salt in brine"
      - field: "brining time"
        value: "7 days instead of 14"
    tags: ["low-sodium", "heart-healthy"]
```

**Guidelines:**

* Each variation should be a distinct recipe version
* Substitutions are ingredient replacements (1:1 or specified ratio)
* Adjustments are changes to steps, temperatures, durations
* Use tags for filtering and discovery

---

## 15. Scaling Guidance (Optional)

Scaling section provides guidelines for adjusting recipe batch sizes.

```yaml
scaling:
  minBatchSize: number
  maxBatchSize: number
  unit: string  # liters, gallons
  linearIngredients:
    - string  # Ingredients that scale proportionally
  nonLinearIngredients:
    - name: string
      scalingFactor: number (optional)
      notes: string
  adjustmentNotes:
    - condition: string  # "when batchSize > 20L"
      adjustment: string  # "increase heating time by 50%"
  recommendations: string (optional)
```

**Example:**

```yaml
scaling:
  minBatchSize: 2
  maxBatchSize: 50
  unit: liters
  linearIngredients:
    - "Milk"
    - "Salt"
    - "Starter culture"
  nonLinearIngredients:
    - name: "Rennet"
      scalingFactor: 0.8
      notes: "Rennet efficiency increases with larger batches - use 80% of linear scale"
    - name: "Calcium chloride"
      notes: "Only needed for pasteurized milk, scales linearly"
  adjustmentNotes:
    - condition: "when batchSize > 20L"
      adjustment: "Increase initial heating time by 30-50% due to larger volume"
    - condition: "when batchSize < 4L"
      adjustment: "Temperature changes happen faster - reduce heating time by 25%"
  recommendations: "For first-time scaling, stay within 50-200% of reference batch size. Large batches (>30L) require commercial equipment."
```

**Guidelines:**

* Specify safe scaling range based on recipe testing
* Identify which ingredients scale linearly (1:1 with batch size)
* Document non-linear ingredients with specific guidance
* Include notes about equipment limitations
* Warn about timing changes for different batch sizes

---

## 16. Metadata & Multi-Language Support (Optional)

Metadata section supports multiple languages and additional context.

```yaml
metadata:
  defaultLanguage: string  # ISO 639-1 code (en, es, fr, etc.)
  translations:
    <language-code>:
      name: string
      description: string (optional)
      ingredients:
        - name: string
          preparation: string (optional)
      steps:
        - title: string
          instructions: string
          notes: string (optional)
  keywords: [string] (optional)
  categories: [string] (optional)
  culturalContext: string (optional)
  historicalNotes: string (optional)
```

**Example:**

```yaml
metadata:
  defaultLanguage: en
  translations:
    es:
      name: "Queso Feta Tradicional"
      description: "Receta tradicional griega de queso feta"
      ingredients:
        - name: "Leche de oveja"
          preparation: "Entera, pasteurizada o cruda"
        - name: "Cultivo mesófilo"
      steps:
        - title: "Calentar la leche"
          instructions: "Calentar lentamente la leche mientras se remueve suavemente."
    fr:
      name: "Feta Traditionnelle"
      description: "Recette grecque traditionnelle de fromage feta"
  keywords: ["feta", "greek", "brined", "sheep milk", "mediterranean"]
  categories: ["Traditional", "European", "Intermediate"]
  culturalContext: "Feta has been made in Greece for thousands of years and holds PDO (Protected Designation of Origin) status in the EU."
  historicalNotes: "The name 'feta' comes from the Italian word 'fetta' meaning slice, referring to the practice of slicing the cheese for storage."
```

**Guidelines:**

* Translations should maintain same step count and order
* Translate ingredient names and key instructions
* Keywords aid in search and discovery
* Cultural context preserves traditional knowledge
* Multiple language support is optional but encouraged

---

## 17. Enhanced Validation

The validation section in steps has been enhanced to support more detailed quality checks.

```yaml
steps:
  - stepNumber: number
    # ... other fields ...
    validation:
      expectedResult: string
      visual:
        description: string (optional)
        referencePhoto: string (optional)  # URL to reference image
      tactile:
        description: string (optional)
      measurement:
        type: PH | TEMPERATURE | TIME | WEIGHT (optional)
        expectedValue: number (optional)
        tolerance: number (optional)
        unit: string (optional)
      commonMistakes:
        - issue: string
          cause: string (optional)
          remedy: string (optional)
```

**Example:**

```yaml
validation:
  expectedResult: "Clean break achieved with clear, greenish whey"
  visual:
    description: "Curd should crack cleanly when knife inserted at 45° angle. Whey should be clear and slightly yellow-green, not milky."
    referencePhoto: "https://example.com/clean-break-reference.jpg"
  tactile:
    description: "Curd feels firm but yielding to gentle pressure, like silken tofu"
  measurement:
    type: PH
    expectedValue: 6.4
    tolerance: 0.2
    unit: "pH units"
  commonMistakes:
    - issue: "Whey is milky and cloudy instead of clear"
      cause: "Curds cut too early before full coagulation"
      remedy: "Wait 5-10 more minutes and check for cleaner break"
    - issue: "Curds are too soft and mushy"
      cause: "Not enough rennet or insufficient coagulation time"
      remedy: "Use fresh rennet and ensure proper temperature"
```

---

## 18. Complete Example

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

## 19. Compliance Levels

OCRS defines three compliance levels to accommodate different use cases:

### Level 1: Minimal Compliance (Required)

An OCRS Level 1 compliant recipe MUST have:

* `spec` version declared (OCRS/1.0)
* `recipe.name` (non-empty string)
* At least one `step` with `stepNumber`, `title`, and `instructions`

**All other fields are optional.**

### Level 2: Recommended Compliance

A Level 2 compliant recipe includes Level 1 requirements plus:

* At least one `ingredient` with `name` and `amount`
* `recipe.difficulty` specified
* `recipe.source` with `type` and `reference`
* Steps include `duration` (either `value` or `condition`)
* Steps include `validation.expectedResult`
* Temperature specified where relevant (`temperature.target`)

### Level 3: Full Compliance

A Level 3 compliant recipe includes Level 2 requirements plus:

* `ingredients[].type` specified for all ingredients
* `steps[].category` specified for all steps
* Temperature ranges (`min`/`max`) where applicable
* `finalProduct` section completed
* `equipment` section listing required tools
* `safety.allergens` specified

### Validation

Recipes can be validated using the compliance levels:

```yaml
# Minimal valid recipe (Level 1)
spec: OCRS/1.0
recipe:
  name: "Quick Paneer"
steps:
  - stepNumber: 1
    title: "Heat milk"
    instructions: "Heat milk to boiling"

# This is valid but not recommended for sharing
```

### Extension Namespaces

Applications may add custom fields using the `x-` prefix to avoid conflicts:

```yaml
spec: OCRS/1.0

recipe:
  name: "Feta"
  # ... standard fields ...

# Application-specific extensions
x-myapp:
  customField: "custom value"
  internalId: 12345
  tags: ["featured", "bestseller"]
```

**Guidelines:**
* Extension fields MUST use `x-` prefix
* Other parsers SHOULD ignore unknown `x-` fields
* Extension fields are not part of official OCRS spec
* Apps should document their extensions publicly

---

## 20. Implementation Guidelines

### For Application Developers

**Parsing OCRS:**
1. Check `spec` version (must be OCRS/1.0)
2. Validate required fields (Level 1 compliance minimum)
3. Gracefully handle missing optional fields
4. Ignore unknown extension fields (`x-*`)

**Exporting OCRS:**
1. Include all populated fields
2. Validate before export (prevent invalid YAML)
3. Target Level 2 or Level 3 compliance
4. Use extension namespace for app-specific data

**Example Parser (TypeScript):**
```typescript
interface OCRSRecipe {
  spec: 'OCRS/1.0';
  recipe: {
    name: string;
    style?: CheeseStyle;
    milkType?: MilkType;
    // ... other fields
  };
  ingredients: Ingredient[];
  steps: Step[];
  aging?: Aging;
  finalProduct?: FinalProduct;
  // Optional sections
  equipment?: Equipment;
  cost?: Cost;
  safety?: Safety;
  media?: Media;
  variations?: Variation[];
  scaling?: Scaling;
  metadata?: Metadata;
}

function parseOCRS(yaml: string): OCRSRecipe {
  const parsed = YAML.parse(yaml);

  // Validate spec version
  if (!parsed.spec?.startsWith('OCRS/')) {
    throw new Error('Invalid or missing spec version');
  }

  // Validate required fields
  if (!parsed.recipe?.name) {
    throw new Error('Recipe name is required');
  }

  if (!parsed.steps || parsed.steps.length === 0) {
    throw new Error('At least one step is required');
  }

  return parsed as OCRSRecipe;
}
```

### For Recipe Authors

**Writing OCRS Recipes:**
1. Start with a template (use the Complete Example)
2. Fill required fields first (name, steps)
3. Add ingredients with types
4. Include validation points in steps
5. Document equipment needs
6. Add photos if available
7. Specify allergens and safety info

**Quality Checklist:**
- [ ] Recipe name is descriptive and unique
- [ ] All ingredients have types (PRIMARY, CULTURE, etc.)
- [ ] Steps have validation.expectedResult
- [ ] Temperature and duration specified
- [ ] Equipment listed
- [ ] Allergens documented
- [ ] Final product described
- [ ] Photos or videos linked (if available)

---

## 21. License

This specification is released under **Creative Commons Attribution 4.0 (CC-BY 4.0)**.

Anyone may use, extend, and implement this specification with attribution.

**Attribution Requirements:**
* Credit "Open Cheese Recipe Specification (OCRS)" in your application
* Link to the specification repository or documentation
* Indicate if you've made modifications or extensions

---

## 22. Governance

OCRS is community-maintained. Changes are proposed via public discussion and accepted through versioned releases.

The maintainer acts as steward, not owner, of the specification.

### Contributing

To propose changes or additions:

1. **Minor clarifications** - Submit as issues or pull requests
2. **New optional fields** - Propose for next minor version (v1.2)
3. **Breaking changes** - Discuss extensively, consider for v2.0
4. **Extensions** - Implement using `x-` namespace, share publicly

### Version Roadmap

**v1.0** (Current) - Comprehensive specification with all core and optional sections
**v1.1** (Planned) - Minor refinements based on community feedback
**v2.0** (Future) - Potential breaking changes after ecosystem adoption

---

## 23. Appendix: Quick Reference

### Required Fields (Level 1)

```yaml
spec: OCRS/1.0
recipe:
  name: string
steps:
  - stepNumber: number
    title: string
    instructions: string
```

### Recommended Fields (Level 2)

Add to Level 1:
```yaml
recipe:
  difficulty: BEGINNER | INTERMEDIATE | ADVANCED
  source:
    type: BOOK | VIDEO | WEBSITE | PERSONAL | COMMUNITY | OTHER
    reference: string
ingredients:
  - name: string
    amount: number
    unit: string
steps:
  - duration:
      type: FIXED | UNTIL_CONDITION
      value: number
      unit: string
    validation:
      expectedResult: string
```

### Full Fields (Level 3)

Add to Level 2:
```yaml
recipe:
  style: FRESH | SOFT | SEMI_SOFT | SEMI_HARD | HARD | BLUE | PASTA_FILATA | WASHED_RIND | OTHER
  milkType: COW | GOAT | SHEEP | BUFFALO | CAMEL | YAK | MIXED | OTHER
ingredients:
  - type: PRIMARY | CULTURE | COAGULANT | ADDITIVE | SEASONING | OTHER
steps:
  - category: string
    temperature:
      target: number
      min: number
      max: number
      unit: C | F
finalProduct:
  texture: string
  flavor: string
  moisture: HIGH | MEDIUM | LOW
equipment:
  required: [...]
safety:
  allergens: [...]
```

### Enumerations Quick Reference

**CheeseStyle:** FRESH, SOFT, SEMI_SOFT, SEMI_HARD, HARD, BLUE, PASTA_FILATA, WASHED_RIND, OTHER

**MilkType:** COW, GOAT, SHEEP, BUFFALO, CAMEL, YAK, MIXED, OTHER

**Difficulty:** BEGINNER, INTERMEDIATE, ADVANCED

**IngredientType:** PRIMARY, CULTURE, COAGULANT, ADDITIVE, SEASONING, OTHER

**DurationType:** FIXED, UNTIL_CONDITION

**Moisture:** HIGH, MEDIUM, LOW

**Allergen:** MILK, LACTOSE, NUTS, SOY, EGGS, GLUTEN, OTHER

**Platform:** YOUTUBE, VIMEO, OTHER

---

**Document Version:** 1.0
**Last Updated:** January 19, 2026
**Status:** Production Ready
