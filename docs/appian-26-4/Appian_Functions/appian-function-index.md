# Appian Function and SAIL Index

## Table of contents

1. [Purpose](#purpose)
2. [How to use this index](#how-to-use-this-index)
3. [Official source map](#official-source-map)
4. [High-risk AI generation areas](#high-risk-ai-generation-areas)
5. [Function quick index](#function-quick-index)
6. [SAIL component quick index](#sail-component-quick-index)
7. [Design system quick index](#design-system-quick-index)
8. [Verification workflow](#verification-workflow)
9. [References](#references)

## Purpose

This index provides a navigation layer for the APN Appian 26.4 functions and SAIL documentation package.

Use it to quickly identify which local guide to use, which official Appian page to verify against, and which AI-generated patterns require extra review before code is pasted into Appian Designer.

## How to use this index

| Need | Use this file | Then verify against |
|---|---|---|
| Appian function usage guidance | `functions.md` | Official All Functions page |
| SAIL component and UI guidance | `sail-components.md` | Official Interface Components page |
| AI-safe review checklist | `../design-engine/AI_REVIEW_GUARDRAILS_APPIAN.md` | Function and component docs |
| Interface performance guidance | `../design-engine/03_SAIL_Interface_Standards.md` | Official interface performance page |
| Refresh behaviour | `../design-engine/03_SAIL_Interface_Standards.md` | Official refresh behaviour page |
| Mobile-safe UI | `sail-components.md` | SAIL Recipes and Design System pages |

## Official source map

| Official topic | URL |
|---|---|
| All Appian functions | https://docs.appian.com/suite/help/26.4/Appian_Functions.html |
| Function and component versions | https://docs.appian.com/suite/help/26.4/function_versions.html |
| Interface components | https://docs.appian.com/suite/help/26.4/SAIL_Components.html |
| SAIL recipes | https://docs.appian.com/suite/help/26.4/SAIL_Recipes.html |
| Interface performance | https://docs.appian.com/suite/help/26.4/interface-performance.html |
| Styled icons | https://docs.appian.com/suite/help/26.4/sail/ux-styled-icons.html |
| Chart colour schemes | https://docs.appian.com/suite/help/26.4/Chart_Color_Scheme.html |
| Translation strings | https://docs.appian.com/suite/help/26.4/reference-translation-strings.html |
| Refresh behaviour | https://docs.appian.com/suite/help/26.4/refresh-behavior-interfaces.html |

## High-risk AI generation areas

| Area | Why it is high risk | Review action |
|---|---|---|
| Rich text styling | AI often writes CSS values such as `bold`. | Verify exact Appian style values, such as `STRONG` where supported. |
| Button styling | AI often writes web framework values such as `primary` or `secondary`. | Use only documented Appian button style values. |
| Grid parameters | AI often copies parameters across unrelated components. | Verify every grid parameter on the official grid component page. |
| Query fields | AI often omits fields in record queries. | Require explicit field selection for any field read by caller. |
| Looping | AI often queries inside loops. | Query once, transform with Appian list functions. |
| Integration responses | AI often assumes typed objects. | Treat response as dictionary unless explicitly mapped. |
| Mobile layout | AI often creates desktop-only UI. | Review stacking, width, navigation and grid behaviour. |
| Old function versions | AI may modernise old signatures incorrectly. | Check function versions page before refactoring. |

## Function quick index

| Function or function family | Local guide section | Review focus |
|---|---|---|
| `a!localVariables()` | `functions.md` | Local state, scope, modern expression pattern. |
| `a!refreshVariable()` | `functions.md` | Deliberate refresh behaviour and performance. |
| `a!save()` | `functions.md` | Correct saveInto usage and audit stamping. |
| `a!forEach()` | `functions.md` | Function variables and no query-in-loop. |
| `index()` | `functions.md` | Safe list, map and dictionary access. |
| `wherecontains()` | `functions.md` | Matching value types and empty result handling. |
| `length()` | `functions.md` | Null and empty-list handling. |
| `joinarray()` | `functions.md` | Display-only concatenation use cases. |
| `a!map()` | `functions.md` | Structured local and integration response values. |
| `a!pagingInfo()` | `functions.md` | Paging, batch size and sort placement. |
| `a!sortInfo()` | `functions.md` | Sort field and placement in paging. |
| `a!queryFilter()` | `functions.md` | Field, operator, value type and null guard. |
| `a!queryLogicalExpression()` | `functions.md` | Complex filter grouping. |
| `a!relatedRecordData()` | `functions.md` | Relationship data limits and performance. |
| `a!selectionFields()` | `functions.md` | Selection configuration and return shape. |
| `a!aggregationFields()` | `functions.md` | Aggregation and chart/report usage. |
| `a!queryRecordType()` | `functions.md` | Field selection, return shape, paging and filters. |
| `a!queryRecordByIdentifier()` | `functions.md` | Single-record retrieval and missing-record behaviour. |
| `a!queryEntity()` | `functions.md` | Legacy or DSE-specific usage only where required. |

## SAIL component quick index

| Component area | Local guide section | Review focus |
|---|---|---|
| Forms | `sail-components.md` | Title/header, contents, buttons and submit behaviour. |
| Columns | `sail-components.md` | Responsive stacking and major page structure. |
| Side-by-side layouts | `sail-components.md` | Inline grouping rather than major structure. |
| Pane layouts | `sail-components.md` | Desktop list/detail and phone behaviour. |
| Sections and cards | `sail-components.md` | Semantic grouping and empty states. |
| Text inputs | `sail-components.md` | Value, saveInto, validation and refresh behaviour. |
| Dropdowns and choices | `sail-components.md` | Choice labels, values and reference data. |
| Buttons | `sail-components.md` | Appian-supported styles and no JavaScript events. |
| Rich text | `sail-components.md` | Correct style values and no CSS/HTML. |
| Icons | `sail-components.md` | Meaning, accessibility and no colour-only signals. |
| Grids | `sail-components.md` | Paging, fields, empty state and mobile usability. |
| Charts | `sail-components.md` | Labels, colour scheme, empty state and drill-through. |
| Pickers | `sail-components.md` | Suggestion source, security and performance. |
| Document components | `sail-components.md` | Folder security and file handling. |
| Navigation | `sail-components.md` | Site, portal and record navigation patterns. |

## Design system quick index

| Topic | Review focus |
|---|---|
| Page width | Avoid stretched layouts on large monitors and cramped layouts on mobile. |
| Mobile considerations | Confirm stacking, button behaviour, grids and navigation. |
| Accessibility | Use labels, headings, accessibility text and clear validation messages. |
| Styled icons | Use icons to support meaning, not as the only signal. |
| Chart colours | Use consistent chart colour schemes and readable labels. |
| Translation strings | Avoid repeated hardcoded strings where localisation is required. |
| Refresh behaviour | Use explicit refresh strategies for local variables and queries. |

## Verification workflow

Before accepting AI-generated Appian code:

```text
1. Identify every Appian function used.
2. Identify every SAIL component used.
3. Identify every named parameter used.
4. Identify every allowed value used.
5. Check all four against official Appian 26.4 documentation.
6. Reject anything that looks like Java, JavaScript, React, HTML, CSS or Python inside SAIL.
7. Confirm record queries select every field the caller reads.
8. Confirm null handling for filters, dates, lists and integration responses.
9. Confirm responsive and accessibility behaviour.
10. Confirm unit tests and end-to-end test scenarios exist.
```

## References

Official Appian documentation:

- https://docs.appian.com/suite/help/26.4/Appian_Functions.html
- https://docs.appian.com/suite/help/26.4/function_versions.html
- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/SAIL_Recipes.html
- https://docs.appian.com/suite/help/26.4/interface-performance.html
- https://docs.appian.com/suite/help/26.4/sail/ux-styled-icons.html
- https://docs.appian.com/suite/help/26.4/Chart_Color_Scheme.html
- https://docs.appian.com/suite/help/26.4/reference-translation-strings.html
- https://docs.appian.com/suite/help/26.4/refresh-behavior-interfaces.html
