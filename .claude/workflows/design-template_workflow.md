# Workflow: design-template

## Overview
Creates a slide design template by researching a company's brand or applying a default monotone style.

## Flow

```
User Request
    │
    ▼
┌─────────────────────┐
│  design-template     │  (Skill: router)
│  skill               │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│  design-template-    │  (Agent: planner)
│  planner             │
│                      │
│  Decides:            │
│  - Research needed?  │
│  - Style direction   │
└─────────┬───────────┘
          │
    ┌─────┴──────┐
    │            │
    ▼            ▼
┌────────┐  ┌────────────────┐
│ brand- │  │ template-      │
│ researcher│  │ generator      │
│(optional)│  │ (always runs)  │
└────┬───┘  └───────┬────────┘
     │              │
     └──────┬───────┘
            │
            ▼
    Updated template.js
```

## Execution Steps

### Step 1: Input Collection (Planner)
- Parse user request for: company name, URL, style keywords
- If no company info → set style to "monotone", skip Step 2

### Step 2: Brand Research (Conditional)
- Agent: `brand-researcher`
- Input: company_name, company_url
- Output: brand colors, font, tone
- On failure: fall back to monotone

### Step 3: Template Generation (Always)
- Agent: `template-generator`
- Input: brand research results OR monotone defaults
- Output: COLORS, CHART_COLORS, FACE values
- Action: Edit `scripts/template.js` with new values

### Step 4: Confirmation
- Show the user the new color palette
- Ask for approval before finalizing
- If rejected, allow adjustments

## Data Flow
```
User Input
  → { company_name, company_url, style_preference }

brand-researcher output
  → { found, brand: { primary_color, secondary_color, ... } }

template-generator output
  → { colors: { PRIMARY, SECONDARY, ... }, chart_colors: [...], font_face }

Final
  → Updated scripts/template.js
```

## Error Handling
- Website unreachable → fall back to monotone
- No brand colors found → fall back to monotone
- User rejects template → ask for specific color preferences → re-run template-generator
