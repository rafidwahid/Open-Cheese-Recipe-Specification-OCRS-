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
- **HACCP (Hazard Analysis Critical Control Point) compliance**
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
* `haccp` – HACCP compliance and critical control points
* `productionRecords` – Audit-ready batch production and quality control documentation
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

## 13. HACCP Compliance (Optional)

HACCP (Hazard Analysis and Critical Control Points) section enables food safety compliance for commercial cheese production.

```yaml
haccp:
  enabled: boolean
  hazardAnalysis:
    biologicalHazards:
      - hazard: string
        controlMeasure: string
        severity: LOW | MEDIUM | HIGH | CRITICAL
    chemicalHazards:
      - hazard: string
        controlMeasure: string
        severity: LOW | MEDIUM | HIGH | CRITICAL
    physicalHazards:
      - hazard: string
        controlMeasure: string
        severity: LOW | MEDIUM | HIGH | CRITICAL
  criticalControlPoints:
    - ccpNumber: number
      stepNumber: number (optional)  # Links to recipe step
      processStep: string
      hazard: string
      criticalLimit:
        parameter: TEMPERATURE | PH | TIME | WEIGHT | AW | OTHER
        value: number | string
        unit: string (optional)
        operator: EQUAL | LESS_THAN | GREATER_THAN | BETWEEN | NOT_EQUAL
        range:
          min: number (optional)
          max: number (optional)
      monitoringProcedure:
        what: string  # What to monitor
        how: string  # How to monitor
        frequency: string  # When/how often
        who: string  # Who is responsible
      correctiveActions:
        - action: string
          responsibility: string (optional)
      verification:
        method: string
        frequency: string
      recordKeeping:
        formName: string (optional)
        retentionPeriod: string  # "2 years", "indefinitely"
        notes: string (optional)
  prerequisitePrograms:
    - program: string  # "Sanitation", "Pest Control", "Equipment Maintenance"
      description: string
      frequency: string (optional)
  traceability:
    lotNumberFormat: string (optional)  # "YYMMDD-BATCH-##"
    requiredDocumentation: [string] (optional)
    upstreamTracing: string (optional)  # Milk source tracing
    downstreamTracing: string (optional)  # Distribution tracking
```

**Example:**

```yaml
haccp:
  enabled: true
  hazardAnalysis:
    biologicalHazards:
      - hazard: "Pathogenic bacteria (Listeria monocytogenes, E. coli O157:H7, Salmonella)"
        controlMeasure: "Pasteurization at 72°C for 15 seconds OR sourcing pasteurized milk"
        severity: CRITICAL
      - hazard: "Pathogen growth during aging"
        controlMeasure: "Maintain proper temperature (10-12°C) and monitor pH"
        severity: HIGH
    chemicalHazards:
      - hazard: "Antibiotic residues in milk"
        controlMeasure: "Source milk from approved suppliers with testing certificates"
        severity: MEDIUM
      - hazard: "Cleaning chemical contamination"
        controlMeasure: "Thorough rinsing of equipment after sanitation"
        severity: MEDIUM
    physicalHazards:
      - hazard: "Metal fragments from equipment"
        controlMeasure: "Visual inspection, metal detection (if available)"
        severity: MEDIUM
      - hazard: "Foreign objects (hair, plastic)"
        controlMeasure: "Personnel hygiene, covering hair, good manufacturing practices"
        severity: LOW

  criticalControlPoints:
    - ccpNumber: 1
      stepNumber: 1
      processStep: "Milk Pasteurization (or receipt of pasteurized milk)"
      hazard: "Pathogenic bacteria survival"
      criticalLimit:
        parameter: TEMPERATURE
        value: 72
        unit: "°C"
        operator: GREATER_THAN
        range:
          min: 72
      monitoringProcedure:
        what: "Pasteurization temperature"
        how: "Calibrated thermometer, continuous monitoring"
        frequency: "Every batch, real-time during pasteurization"
        who: "Production supervisor or designated operator"
      correctiveActions:
        - action: "If temperature < 72°C, continue heating until 72°C reached for 15 seconds"
          responsibility: "Production supervisor"
        - action: "If unable to achieve temperature, reject milk batch and document"
          responsibility: "Production supervisor"
        - action: "Re-pasteurize if time/temperature not met"
          responsibility: "Production supervisor"
      verification:
        method: "Calibrate thermometer weekly; Review records daily"
        frequency: "Thermometer calibration: Weekly. Records review: Daily"
      recordKeeping:
        formName: "Pasteurization Log"
        retentionPeriod: "2 years minimum (per FDA/local regulations)"
        notes: "Record batch ID, date, time, temperature, duration, operator name"

    - ccpNumber: 2
      stepNumber: 3
      processStep: "Curd Acidification"
      hazard: "Pathogen survival due to insufficient acidification"
      criticalLimit:
        parameter: PH
        value: 5.3
        operator: LESS_THAN
        range:
          max: 5.3
      monitoringProcedure:
        what: "pH of curd/whey"
        how: "Calibrated pH meter"
        frequency: "At end of cooking step, before draining"
        who: "Cheesemaker or lab technician"
      correctiveActions:
        - action: "If pH > 5.3, continue incubation and re-test every 30 minutes"
          responsibility: "Cheesemaker"
        - action: "If pH does not drop after 2 hours, reject batch or re-inoculate with culture"
          responsibility: "Production manager"
        - action: "Document deviation and corrective action taken"
          responsibility: "Cheesemaker"
      verification:
        method: "Calibrate pH meter daily; Lab verification of select batches"
        frequency: "pH meter calibration: Daily. Lab testing: Weekly random samples"
      recordKeeping:
        formName: "pH Monitoring Log"
        retentionPeriod: "2 years"
        notes: "Record batch ID, pH reading, time, operator, any corrective actions"

    - ccpNumber: 3
      stepNumber: 6
      processStep: "Aging Temperature Control"
      hazard: "Pathogen growth (Listeria) during aging"
      criticalLimit:
        parameter: TEMPERATURE
        value: 12
        unit: "°C"
        operator: LESS_THAN
        range:
          max: 12
      monitoringProcedure:
        what: "Aging room/cave temperature"
        how: "Continuous data logger or manual thermometer readings"
        frequency: "Continuous (data logger) OR every 4 hours (manual)"
        who: "Aging room supervisor"
      correctiveActions:
        - action: "If temp > 12°C, adjust refrigeration immediately"
          responsibility: "Aging room supervisor"
        - action: "If temp exceeded for >4 hours, conduct microbiological testing"
          responsibility: "Quality manager"
        - action: "Evaluate cheese safety before release"
          responsibility: "Quality manager"
      verification:
        method: "Review temperature logs daily; Environmental swab testing monthly"
        frequency: "Logs: Daily. Swabs: Monthly"
      recordKeeping:
        formName: "Aging Room Temperature Log"
        retentionPeriod: "2 years"
        notes: "Continuous digital records OR manual log with date/time/temp/operator"

    - ccpNumber: 4
      processStep: "Finished Product Testing (before release)"
      hazard: "Pathogenic bacteria in finished cheese"
      criticalLimit:
        parameter: OTHER
        value: "Negative for Listeria monocytogenes, E. coli O157:H7, Salmonella"
        operator: EQUAL
      monitoringProcedure:
        what: "Microbiological testing of finished cheese"
        how: "Lab analysis (in-house or third-party certified lab)"
        frequency: "Every batch OR statistically valid sampling plan (e.g., 10% of batches)"
        who: "Quality assurance manager"
      correctiveActions:
        - action: "If positive test, hold entire lot; conduct investigation"
          responsibility: "Quality manager"
        - action: "Destroy affected product or re-pasteurize if possible"
          responsibility: "Production manager"
        - action: "Investigate root cause (HACCP failure, contamination source)"
          responsibility: "HACCP coordinator"
        - action: "Implement corrective measures to prevent recurrence"
          responsibility: "Management team"
      verification:
        method: "Third-party lab verification quarterly; Review all test results"
        frequency: "Quarterly independent verification"
      recordKeeping:
        formName: "Finished Product Testing Log"
        retentionPeriod: "2 years minimum (or per local regulations)"
        notes: "Batch ID, test date, lab results, pass/fail, corrective actions if failed"

  prerequisitePrograms:
    - program: "Sanitation Standard Operating Procedures (SSOP)"
      description: "Daily cleaning and sanitizing of all food contact surfaces, equipment, and utensils using approved sanitizers"
      frequency: "Daily before production and after each batch"
    - program: "Good Manufacturing Practices (GMP)"
      description: "Personnel hygiene (handwashing, clean clothing, hair nets), facility maintenance, pest control"
      frequency: "Continuous; Personnel training quarterly"
    - program: "Supplier Approval Program"
      description: "Verify all milk suppliers provide pasteurized milk OR have HACCP plans; Request Certificates of Analysis"
      frequency: "Annual supplier audits; COA with each delivery"
    - program: "Equipment Calibration and Maintenance"
      description: "Regular calibration of thermometers, pH meters, scales; Preventive maintenance schedule"
      frequency: "Thermometers/pH meters: Weekly calibration. Equipment maintenance: Per manufacturer schedule"
    - program: "Pest Control"
      description: "Contracted pest control service; Monthly inspections and treatments"
      frequency: "Monthly inspections; Quarterly treatments"
    - program: "Water Quality Testing"
      description: "If using well water, annual testing for potability; Municipal water supply verification"
      frequency: "Annually (well water) OR verify municipal reports quarterly"

  traceability:
    lotNumberFormat: "YYMMDD-CHEESE-BATCH##"
    requiredDocumentation:
      - "Milk supplier invoices with lot numbers"
      - "Pasteurization logs"
      - "Culture supplier batch numbers"
      - "Rennet supplier batch numbers"
      - "pH logs"
      - "Temperature logs (pasteurization and aging)"
      - "Finished product test results"
      - "Distribution records (customer, date, quantity, lot number)"
    upstreamTracing: "One step back: Trace to specific milk supplier delivery date and farm source"
    downstreamTracing: "One step forward: Trace to specific customer/retailer and delivery date"
```

**Guidelines:**

* **For Commercial Producers**: HACCP is mandatory for regulatory compliance (FDA, USDA, EU regulations)
* **For Home Cheesemakers**: HACCP is optional but good practice for food safety awareness
* **Critical Control Points**: Focus on temperature (pasteurization, aging), pH (acidification), and pathogen testing
* **Record Keeping**: Maintain detailed logs for all CCPs; retention period varies by jurisdiction (typically 2+ years)
* **Verification**: Regular calibration of instruments, third-party testing, management review
* **Corrective Actions**: Document all deviations and actions taken
* **Traceability**: Essential for recall capability (trace back to ingredients, trace forward to customers)

**Common Cheese CCPs:**
1. **Pasteurization** (if using raw milk → pasteurized)
2. **pH Control** (acidification to prevent pathogen growth)
3. **Aging Temperature** (especially critical for soft/fresh cheeses)
4. **Finished Product Testing** (pathogen testing before release)
5. **Cooling Rate** (for fresh cheeses, cool to <4°C within X hours)

**Regulatory References:**
- FDA 21 CFR Part 117 (FSMA - Food Safety Modernization Act)
- FDA Grade A Pasteurized Milk Ordinance (PMO)
- EU Regulation (EC) No 852/2004 (Hygiene of foodstuffs)
- Codex Alimentarius HACCP Guidelines

---

## 14. Production Records & Audit Documentation (Optional)

Production Records section provides audit-ready documentation structure for batch tracking, quality control, and regulatory compliance.

```yaml
productionRecords:
  batchTracking:
    lotNumberPrefix: string  # Recipe-specific lot number prefix
    batchNumberFormat: string  # Format template (e.g., "YYMMDD-FETA-###")
    productionLogTemplate: string  # Name of production log form
    requiredDataPoints:
      - dataPoint: string  # "Milk temperature", "pH at coagulation", etc.
        unit: string (optional)
        acceptableRange:
          min: number (optional)
          max: number (optional)
        recordingFrequency: string  # "Every batch", "Hourly", "Per step"

  qualityControl:
    inspectionPoints:
      - stepNumber: number (optional)  # Links to recipe step
        inspectionType: VISUAL | MEASUREMENT | SENSORY | MICROBIOLOGICAL | OTHER
        parameter: string  # "Curd firmness", "Whey clarity", "pH", "Temperature"
        acceptanceCriteria: string
        rejectionCriteria: string (optional)
        samplingPlan: string (optional)  # "100% inspection", "Random 10%", etc.
        responsibleRole: string
        documentationRequired: boolean

    deviationManagement:
      reportingThreshold: string  # "Any deviation", "Out of spec by >10%"
      documentationForm: string  # "Deviation Report Form DR-001"
      approvalRequired: boolean
      retentionPeriod: string

  regulatoryCompliance:
    certifications:
      - certificationType: ORGANIC | KOSHER | HALAL | PDO | PGI | GMP | HACCP | OTHER
        certifyingBody: string
        certificateNumber: string (optional)
        validUntil: string (optional)  # ISO 8601 date
        specificRequirements: [string] (optional)

    labelingRequirements:
      productName: string  # Official product name for label
      ingredients: [string]  # Ingredient declaration order (by weight)
      allergenStatement: string
      nutritionFactsRequired: boolean
      countryOfOrigin: string (optional)
      additionalStatements: [string] (optional)  # "Contains live cultures", etc.

    holdAndRelease:
      holdPeriod: string (optional)  # "24 hours", "Until test results"
      releaseChecklist: [string] (optional)
      releaseAuthority: string  # "Quality Manager", "Lab Supervisor"

  auditTrail:
    criticalActions:
      - action: string  # "Recipe approval", "Batch release", "Deviation approval"
        requiresSignature: boolean
        requiresTimestamp: boolean
        requiresReason: boolean (optional)
        approvalLevel: string  # "Supervisor", "Manager", "Quality Director"

    recordRetention:
      productionLogs: string  # "2 years", "3 years", "Indefinitely"
      qualityRecords: string
      deviationReports: string
      haccpRecords: string
      batchReleaseDocuments: string
      complaintRecords: string (optional)

  traceabilityLinks:
    rawMaterialTracking:
      - material: string  # "Milk", "Starter culture", "Rennet"
        supplierRequired: boolean
        lotNumberRequired: boolean
        receiptDateRequired: boolean
        certificateOfAnalysisRequired: boolean

    productionEnvironment:
      - environmentFactor: string  # "Room temperature", "Equipment ID", "Operator ID"
        recordingRequired: boolean
        acceptableRange: string (optional)
```

**Example:**

```yaml
productionRecords:
  batchTracking:
    lotNumberPrefix: "FTA"  # Feta cheese prefix
    batchNumberFormat: "YYMMDD-FTA-###"
    productionLogTemplate: "Feta Production Log v2.3"
    requiredDataPoints:
      - dataPoint: "Milk receipt temperature"
        unit: "°C"
        acceptableRange:
          min: 2
          max: 6
        recordingFrequency: "Every milk delivery"

      - dataPoint: "Pasteurization temperature"
        unit: "°C"
        acceptableRange:
          min: 72
          max: 75
        recordingFrequency: "Continuous during pasteurization"

      - dataPoint: "Coagulation pH"
        acceptableRange:
          min: 6.3
          max: 6.6
        recordingFrequency: "Every batch before rennet addition"

      - dataPoint: "Curd pH at draining"
        acceptableRange:
          max: 5.3
        recordingFrequency: "Every batch before draining"

      - dataPoint: "Aging room temperature"
        unit: "°C"
        acceptableRange:
          min: 8
          max: 12
        recordingFrequency: "Every 4 hours (or continuous data logger)"

  qualityControl:
    inspectionPoints:
      - stepNumber: 2
        inspectionType: VISUAL
        parameter: "Milk quality upon receipt"
        acceptanceCriteria: "No off-odors, no visible contamination, temperature 2-6°C"
        rejectionCriteria: "Sour smell, visible particles, temperature >6°C"
        samplingPlan: "100% sensory check, temperature of every delivery"
        responsibleRole: "Receiving clerk or production supervisor"
        documentationRequired: true

      - stepNumber: 3
        inspectionType: MEASUREMENT
        parameter: "Coagulation time and clean break test"
        acceptanceCriteria: "Clean break achieved within 45-60 minutes, whey is clear and greenish"
        rejectionCriteria: "No clean break after 90 minutes, whey is milky"
        samplingPlan: "Every batch"
        responsibleRole: "Cheesemaker"
        documentationRequired: true

      - stepNumber: 6
        inspectionType: MEASUREMENT
        parameter: "Final curd pH"
        acceptanceCriteria: "pH ≤ 5.3"
        rejectionCriteria: "pH > 5.5"
        samplingPlan: "Every batch"
        responsibleRole: "Cheesemaker or QC technician"
        documentationRequired: true

      - stepNumber: null  # Final product
        inspectionType: MICROBIOLOGICAL
        parameter: "Finished product pathogen testing"
        acceptanceCriteria: "Negative for Listeria, E. coli O157:H7, Salmonella"
        rejectionCriteria: "Positive for any pathogen"
        samplingPlan: "Every batch OR 10% random sampling (per approved plan)"
        responsibleRole: "Quality Assurance Manager"
        documentationRequired: true

      - stepNumber: null
        inspectionType: SENSORY
        parameter: "Finished product taste and texture evaluation"
        acceptanceCriteria: "Crumbly texture, salty-tangy flavor, white color, no off-flavors"
        rejectionCriteria: "Slimy texture, bitter taste, off-odors"
        samplingPlan: "Every batch"
        responsibleRole: "Cheesemaker or QC panel"
        documentationRequired: true

    deviationManagement:
      reportingThreshold: "Any deviation from critical limits (pH, temperature) OR quality defect affecting >10% of batch"
      documentationForm: "Deviation Report Form DR-001"
      approvalRequired: true
      retentionPeriod: "3 years minimum"

  regulatoryCompliance:
    certifications:
      - certificationType: ORGANIC
        certifyingBody: "USDA National Organic Program"
        certificateNumber: "ORG-12345"
        validUntil: "2026-12-31"
        specificRequirements:
          - "Use only certified organic milk from approved suppliers"
          - "No synthetic additives or preservatives"
          - "Maintain organic separation from non-organic products"

      - certificationType: HACCP
        certifyingBody: "Internal HACCP Plan (FDA FSMA compliant)"
        specificRequirements:
          - "Follow HACCP plan version 3.2 dated 2025-01-15"
          - "Monitor all CCPs per HACCP schedule"
          - "Conduct annual HACCP plan review"

    labelingRequirements:
      productName: "Traditional Greek-Style Feta Cheese"
      ingredients:
        - "Pasteurized sheep milk"
        - "Salt"
        - "Cheese cultures"
        - "Microbial rennet"
      allergenStatement: "Contains: Milk. May contain traces of lactose."
      nutritionFactsRequired: true
      countryOfOrigin: "Made in USA"
      additionalStatements:
        - "Keep refrigerated"
        - "Best when stored in brine"
        - "Contains live and active cultures"

    holdAndRelease:
      holdPeriod: "48 hours minimum (until microbiological test results available)"
      releaseChecklist:
        - "All production records complete and signed"
        - "HACCP CCP monitoring records reviewed and approved"
        - "pH test results within specification (≤5.3)"
        - "Microbiological test results negative for pathogens"
        - "Sensory evaluation passed"
        - "Lot number applied to all packages"
        - "Temperature during hold period maintained at 4-10°C"
      releaseAuthority: "Quality Assurance Manager"

  auditTrail:
    criticalActions:
      - action: "Recipe approval or modification"
        requiresSignature: true
        requiresTimestamp: true
        requiresReason: true
        approvalLevel: "Quality Director and Production Manager"

      - action: "Batch production start"
        requiresSignature: true
        requiresTimestamp: true
        requiresReason: false
        approvalLevel: "Production Supervisor"

      - action: "Deviation from CCP critical limit"
        requiresSignature: true
        requiresTimestamp: true
        requiresReason: true
        approvalLevel: "Production Supervisor (immediate); Quality Manager (final approval)"

      - action: "Batch release for distribution"
        requiresSignature: true
        requiresTimestamp: true
        requiresReason: false
        approvalLevel: "Quality Assurance Manager"

      - action: "Batch rejection or disposal"
        requiresSignature: true
        requiresTimestamp: true
        requiresReason: true
        approvalLevel: "Quality Manager and Production Manager"

    recordRetention:
      productionLogs: "3 years from production date"
      qualityRecords: "3 years from production date"
      deviationReports: "5 years from incident date"
      haccpRecords: "2 years minimum (per FDA); 3 years recommended"
      batchReleaseDocuments: "3 years from release date"
      complaintRecords: "5 years from complaint date"

  traceabilityLinks:
    rawMaterialTracking:
      - material: "Sheep milk"
        supplierRequired: true
        lotNumberRequired: true
        receiptDateRequired: true
        certificateOfAnalysisRequired: true  # Organic cert, pasteurization cert, pathogen testing

      - material: "Mesophilic starter culture"
        supplierRequired: true
        lotNumberRequired: true
        receiptDateRequired: true
        certificateOfAnalysisRequired: true  # Supplier COA with activity test

      - material: "Microbial rennet"
        supplierRequired: true
        lotNumberRequired: true
        receiptDateRequired: true
        certificateOfAnalysisRequired: false

      - material: "Sea salt (food grade)"
        supplierRequired: true
        lotNumberRequired: true
        receiptDateRequired: true
        certificateOfAnalysisRequired: false

    productionEnvironment:
      - environmentFactor: "Aging cave/room identifier"
        recordingRequired: true
        acceptableRange: "Cave 1, 2, or 3 only (temperature-controlled)"

      - environmentFactor: "Primary cheesemaker operator ID"
        recordingRequired: true

      - environmentFactor: "Equipment sanitization completion"
        recordingRequired: true
        acceptableRange: "Sanitization log completed within 2 hours before production start"
```

**Guidelines:**

* **For Regulatory Audits**: This section provides all documentation requirements for FDA, USDA, EU, or other regulatory inspections
* **For Certification Bodies**: Organic, Kosher, Halal certifiers can verify compliance from recipe-level requirements
* **For Quality Management Systems**: Integrates with ISO 9001, SQF, BRC, FSSC 22000 documentation requirements
* **For Traceability**: Enables one-up/one-down traceability for recall situations
* **For Legal Defense**: Documented due diligence in case of product liability claims

**Audit-Ready Data Fields Summary:**

1. **Batch Tracking** - Lot numbers, production logs, required data points
2. **Quality Control** - Inspection points, acceptance criteria, sampling plans
3. **Deviation Management** - How to handle and document out-of-spec situations
4. **Regulatory Compliance** - Certifications, labeling, hold-and-release procedures
5. **Audit Trail** - Who approved what, when, and why (signature requirements)
6. **Record Retention** - How long to keep each type of record (varies by regulation)
7. **Traceability** - Raw material tracking, production environment documentation

**Integration with HACCP:**
- Production Records complement HACCP by adding QC, regulatory, and audit requirements
- HACCP focuses on food safety CCPs
- Production Records cover broader quality, compliance, and traceability needs

---

## 15. Media Attachments (Optional)

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

## 16. Recipe Variations (Optional)

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

## 17. Scaling Guidance (Optional)

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

## 18. Metadata & Multi-Language Support (Optional)

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

## 19. Enhanced Validation

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

## 20. Complete Example

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

## 21. Compliance Levels

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
* `haccp.criticalControlPoints` defined for commercial production (if applicable)

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

## 22. Implementation Guidelines

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
  haccp?: HACCP;
  productionRecords?: ProductionRecords;
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

## 23. License

This specification is released under **Creative Commons Attribution 4.0 (CC-BY 4.0)**.

Anyone may use, extend, and implement this specification with attribution.

**Attribution Requirements:**
* Credit "Open Cheese Recipe Specification (OCRS)" in your application
* Link to the specification repository or documentation
* Indicate if you've made modifications or extensions

---

## 24. Governance

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

## 25. Appendix: Quick Reference

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
haccp:
  criticalControlPoints: [...]  # For commercial production
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

**HACCP Severity:** LOW, MEDIUM, HIGH, CRITICAL

**HACCP Parameter:** TEMPERATURE, PH, TIME, WEIGHT, AW (water activity), OTHER

**HACCP Operator:** EQUAL, LESS_THAN, GREATER_THAN, BETWEEN, NOT_EQUAL

**Inspection Type:** VISUAL, MEASUREMENT, SENSORY, MICROBIOLOGICAL, OTHER

**Certification Type:** ORGANIC, KOSHER, HALAL, PDO, PGI, GMP, HACCP, OTHER

---

**Document Version:** 1.0
**Last Updated:** January 19, 2026
**Status:** Production Ready
