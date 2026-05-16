# 03 SAIL Interface Standards

## Table of contents

1. [Purpose](#purpose)
2. [Source of truth and safe-use rule](#source-of-truth-and-safe-use-rule)
3. [Interface design principles](#interface-design-principles)
4. [UI design workflow](#ui-design-workflow)
5. [Recommended interface structure](#recommended-interface-structure)
6. [Rule inputs and local variables](#rule-inputs-and-local-variables)
7. [Save behaviour](#save-behaviour)
8. [Refresh behaviour](#refresh-behaviour)
9. [Null safety](#null-safety)
10. [Querying from interfaces](#querying-from-interfaces)
11. [SAIL component groups](#sail-component-groups)
12. [Common component input standards](#common-component-input-standards)
13. [Grid standards](#grid-standards)
14. [Chart and reporting standards](#chart-and-reporting-standards)
15. [Icon and visual styling standards](#icon-and-visual-styling-standards)
16. [Translation and localisation standards](#translation-and-localisation-standards)
17. [Performance standards](#performance-standards)
18. [Component guardrails](#component-guardrails)
19. [User display standards](#user-display-standards)
20. [Interface specification block template](#interface-specification-block-template)
21. [Interface test checklist](#interface-test-checklist)
22. [References](#references)

## Purpose

This file defines APN SAIL interface standards for Appian 26.4 projects.

Use it to reduce common AI-generated SAIL mistakes, especially invalid parameters, weak null handling, unsafe saveInto patterns, poor refresh behaviour, inconsistent layout and unsupported component usage.

## Source of truth and safe-use rule

Official Appian documentation remains the source of truth for exact component names, function names, parameters, allowed values, compatibility flags and examples.

Important rule for AI-assisted SAIL:

```text
Do not invent SAIL component parameters.
Do not invent Appian functions.
Do not invent allowed values.
When uncertain, check the official Appian 26.4 documentation before writing code.
```

This document provides design patterns and safe standards. It is not a complete replacement for the official SAIL component reference.

## Interface design principles

| Principle | Standard |
|---|---|
| One clear purpose | Each interface should have one primary job: create, edit, view, search, review or administer. |
| Data before layout | Confirm record inputs, query rules and save behaviour before designing the visual layout. |
| Progressive disclosure | Show the most important information first and hide secondary detail until needed. |
| Null safe by default | Guard all optional inputs, query results, date formatting and conditional comparisons. |
| Fast initial load | Query only what the screen needs on first render. |
| Accessible by default | Use clear labels, helper text, readable sections and keyboard-friendly patterns. |
| Reviewable | Full SAIL examples should be copy-pasteable and not pseudocode. |

## UI design workflow

Recommended design flow:

```text
1. Identify user goal
2. Identify required data inputs
3. Identify records and relationships
4. Identify actions and save behaviour
5. Choose layout pattern
6. Add validation and null safety
7. Add refresh behaviour deliberately
8. Add performance controls
9. Add accessibility and translation considerations
10. Add unit tests and reviewer checklist
```

## Recommended interface structure

A build-ready interface specification should define:

| Area | Detail required |
|---|---|
| Interface name | Example: `APN_UI_CreateClaim`. |
| Purpose | One sentence explaining what the interface does. |
| Usage | Start form, record view, embedded component, site page or portal page. |
| Rule inputs | Name, type, direction and default behaviour. |
| Local variables | Data loaded by the interface and working-copy variables. |
| Layout | Main sections and conditional sections. |
| Components | Inputs, display fields, grids, charts and actions. |
| Save behaviour | What is saved locally and what is written back to rule inputs. |
| Refresh behaviour | Which variables refresh and when. |
| Validation | Required fields, business validation and error messages. |
| Security | Whether any groups affect visibility. |
| Performance | Query size, paging, refresh and lazy-loading considerations. |
| Tests | Unit tests and end-to-end behaviour. |

## Rule inputs and local variables

Interfaces use `ri!`. Process models use `pv!`.

Do not reference `pv!` inside an interface.

Recommended rule input pattern:

```text
ri!parentRecord     Record Type      Input
ri!record           Record Type      Input/Output
ri!isCancelled      Boolean          Output
ri!documents        Document[]       Output
ri!isReadOnly       Boolean          Input
```

Use one top-level `a!localVariables()` for interface state.

```appian
a!localVariables(
  local!workingRecord: ri!record,
  local!isReadOnly: a!defaultValue(ri!isReadOnly, false),
  local!statusOptions: rule!APN_QRY_GetActiveClaimStatuses(),
  local!validationMessages: {},

  a!formLayout(
    titleBar: a!headerTemplateSimple(
      title: "Create Claim",
      secondaryText: "Capture the details required to create a claim"
    ),
    contents: {
      /* interface body */
    }
  )
)
```

## Save behaviour

For process-backed forms, save field edits into a local working copy first. On submit, write the working copy back to `ri!record`.

Field pattern:

```appian
a!textField(
  label: "Claim Reference",
  value: local!workingRecord.claimReference,
  saveInto: local!workingRecord.claimReference,
  required: true,
  readOnly: local!isReadOnly
)
```

Submit pattern:

```appian
a!buttonWidget(
  label: "Submit",
  style: "SOLID",
  submit: true,
  saveInto: {
    a!save(local!workingRecord.updatedBy, loggedInUser()),
    a!save(local!workingRecord.updatedOn, now()),
    a!save(ri!record, local!workingRecord)
  },
  validate: true
)
```

Cancel pattern:

```appian
a!buttonWidget(
  label: "Cancel",
  style: "OUTLINE",
  submit: true,
  saveInto: a!save(ri!isCancelled, true),
  validate: false
)
```

## Refresh behaviour

Use refresh behaviour deliberately. Do not refresh expensive queries on every user interaction unless the behaviour is required.

Recommended approach:

| Scenario | Standard |
|---|---|
| Static reference data | Load once unless the user action changes it. |
| Dependent dropdown | Refresh when the parent selection changes. |
| Search results | Refresh when the user clicks Search or changes filter variables intentionally. |
| Record details | Refresh after save or after a related action where stale data would be misleading. |
| Expensive integration | Refresh only on explicit user action. |

Pattern:

```appian
local!selectedStatus,
local!claims: a!refreshVariable(
  value: rule!APN_QRY_SearchClaims(status: local!selectedStatus),
  refreshOnVarChange: local!selectedStatus
)
```

## Null safety

Do not compare null values directly to typed values.

Recommended:

```appian
local!safeStatus: a!defaultValue(local!workingRecord.status, ""),
showWhen: local!safeStatus = "OPEN"
```

For dates:

```appian
if(
  isnull(local!workingRecord.createdOn),
  "-",
  text(local!workingRecord.createdOn, "dd/MM/yyyy")
)
```

For plain text conversion, use `tostring()` rather than date-formatting functions.

## Querying from interfaces

Query reference data once into a local variable. Do not call the same query rule repeatedly inside multiple components.

```appian
local!statusOptions: rule!APN_QRY_GetActiveClaimStatuses()
```

Then reuse it:

```appian
a!dropdownField(
  label: "Status",
  choiceLabels: local!statusOptions.label,
  choiceValues: local!statusOptions.claimStatusCode,
  value: local!workingRecord.claimStatusCode,
  saveInto: local!workingRecord.claimStatusCode,
  placeholder: "Select status"
)
```

Query rule standards:

- use `a!queryRecordType()` for record data
- specify `fields` for every field the caller reads
- keep `a!sortInfo()` inside `a!pagingInfo()`
- set `fetchTotalCount: true` only when total count is required
- null-guard all filter values used with `=`
- avoid query-in-loop patterns

## SAIL component groups

Use the official component reference for exact syntax. The table below is a design catalogue, not a parameter reference.

| Group | Typical purpose | Examples of usage |
|---|---|---|
| Layouts | Page and section structure | Forms, cards, columns, side-by-side content, panes and sections. |
| Inputs | Capture user data | Text, paragraph, number, decimal, date, date time, dropdown, checkbox, radio, picker and file upload. |
| Display components | Present read-only information | Rich text, images, tags, stamps, milestones, document links and field displays. |
| Buttons and actions | Trigger local saves, submits, record actions or process starts | Primary submit, cancel, search, reset, record action and related action controls. |
| Grids | Display tabular data | Read-only grids, editable grids, selectable rows and action columns. |
| Charts | Display summarised data | Bar, column, line, pie and KPI-style visualisation where supported. |
| Rich text | Styled inline content | Headings, emphasis, links, icons and status labels. |
| Pickers | Select users, groups, records or documents | User selection, group selection, record selection and entity selection where supported. |
| Documents | Upload, link or display documents | Document upload, document download and document metadata presentation. |
| Navigation | Move users between pages or records | Record links, safe links, dynamic links and navigation-style interfaces. |
| Chat and AI components | Conversational or AI-supported interface patterns | Use only where supported in the target environment and confirmed in official docs. |
| Portal-safe components | Public portal user experience | Confirm portal compatibility in official documentation before use. |

## Common component input standards

When specifying a SAIL component, document these inputs where relevant. Do not add a parameter unless the official component reference confirms it exists.

| Input type | Why it matters |
|---|---|
| `label` | User-visible field name. |
| `labelPosition` | Whether label is visible, collapsed or placed according to design. |
| `instructions` | Short helper text where users need guidance. |
| `value` | Current display or input value. |
| `saveInto` | Where user changes are stored. |
| `required` | Whether Appian should enforce completion. |
| `readOnly` | Whether field is view-only. |
| `disabled` | Whether user interaction is blocked. |
| `showWhen` | Conditional visibility for supported components. |
| `validations` | Business validation messages. |
| `placeholder` | Empty state guidance for supported inputs. |
| `choiceLabels` | User-facing labels for choice components. |
| `choiceValues` | Saved values for choice components. |
| `helpTooltip` | Brief contextual help where supported. |

## Grid standards

Grid rules:

- use explicit fields in query rules feeding the grid
- use `pagingInfo` deliberately
- avoid query-in-loop patterns
- use null-safe formatting for dates and numbers
- avoid unsupported grid parameters
- keep actions in a clear actions column
- use record links or record actions rather than custom navigation where possible

Example grid column pattern:

```appian
a!gridColumn(
  label: "Claim Reference",
  value: fv!row[recordType!APN_REC_Claim.fields.claimReference],
  width: "MEDIUM"
)
```

## Chart and reporting standards

Use charts for summarised decision support, not as a replacement for searchable grids.

Chart standards:

| Area | Standard |
|---|---|
| Data preparation | Aggregate data before charting where possible. |
| Labels | Use clear business labels, not raw codes. |
| Colours | Use the approved colour scheme or Appian chart colour schemes. |
| Empty state | Show a meaningful empty message when no data exists. |
| Drill-through | Where users need detail, provide a related grid or record link. |

## Icon and visual styling standards

Use icons to support meaning, not decoration only.

| Use case | Standard |
|---|---|
| Status | Use consistent icon and colour combinations. |
| Actions | Use familiar icons for create, edit, view, approve, reject and cancel. |
| Warnings | Use icons sparingly and pair them with clear text. |
| Accessibility | Do not rely on colour alone to convey meaning. |

## Translation and localisation standards

Where interfaces need multiple languages, keep visible strings easy to externalise.

Recommendations:

- avoid hardcoding repeated labels across many interfaces
- use shared rules or translation string patterns for common labels
- do not concatenate sentences in a way that makes translation difficult
- format dates and numbers according to project requirements
- keep validation messages clear and reusable

## Performance standards

Apply the official interface performance guidance and project-specific performance targets.

| Area | Standard |
|---|---|
| Initial load | Avoid unnecessary queries and integrations on first render. |
| Reference data | Query once and reuse. |
| Grids | Use paging and select only required fields. |
| Loops | Avoid queries inside `a!forEach()`. |
| Refresh | Use `a!refreshVariable()` only where refresh behaviour is required. |
| Integrations | Do not call slow integrations on every keystroke or every refresh. |
| Large data | Use search, filters and pagination rather than loading all rows. |

## Component guardrails

| Area | Standard |
|---|---|
| Form title | Use documented title/header patterns only. |
| Conditional sections | Use `if()` for groups of components. Use `showWhen` only on supported individual components. |
| Dropdown placeholder | Use documented placeholder behaviour. |
| Buttons | Use documented button styles only. |
| Record actions | Use record action components rather than forcing actions into buttons. |
| String special characters | Use `char()` where required. |
| Local variables | Do not scatter unrelated `a!localVariables()` blocks throughout the interface. |
| Unsupported parameters | Remove anything not present in the official component reference. |
| Unsupported nesting | Avoid nesting layouts in ways not supported by Appian. |

## User display standards

Do not show raw usernames where a display name is expected. Use a standard display rule, such as:

```appian
rule!APN_FMT_DisplayUser(local!workingRecord.createdBy)
```

Capture users as:

```appian
loggedInUser()
```

inside interfaces.

## Interface specification block template

```text
Interface Name:     APN_UI_<Purpose>
Description:        [Story #] - One sentence describing the interface.
Usage:              Start form / record view / reusable component / site page / portal page
Inputs:
  ri!parentRecord   Record Type     Input
  ri!record         Record Type     Input/Output
  ri!isCancelled    Boolean         Output
Local Variables:
  local!workingRecord
  local!referenceData
  local!validationMessages
Sections:
  1. Context header
  2. Main details
  3. Supporting information
  4. Actions
Save Behaviour:
  Field edits save to local!workingRecord.
  Submit stamps audit fields and saves local!workingRecord into ri!record.
Refresh Behaviour:
  State which variables refresh and when.
Validation:
  Required fields and business validations.
Performance:
  Query and refresh considerations.
Unit Tests:
  UI-TEST-1 through UI-TEST-n.
```

## Interface test checklist

| Test | Pass or fail |
|---|---|
| Interface loads with blank inputs. |  |
| Interface loads with populated record input. |  |
| Required fields validate. |  |
| Cancel button skips validation where required. |  |
| Submit stamps audit fields. |  |
| Submit writes local working copy back to `ri!record`. |  |
| Null dates render safely. |  |
| Dropdowns load once and show correct labels. |  |
| Grids render empty state correctly. |  |
| Charts render empty state correctly. |  |
| Usernames render with display rule. |  |
| Refresh behaviour is intentional and tested. |  |
| Interface remains usable on smaller screens where required. |  |
| No unsupported component parameters are present. |  |

## References

Official Appian 26.4 documentation remains the source of truth:

- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/SAIL_Recipes.html
- https://docs.appian.com/suite/help/26.4/interface-performance.html
- https://docs.appian.com/suite/help/26.4/sail/ux-styled-icons.html
- https://docs.appian.com/suite/help/26.4/Chart_Color_Scheme.html
- https://docs.appian.com/suite/help/26.4/reference-translation-strings.html
- https://docs.appian.com/suite/help/26.4/refresh-behavior-interfaces.html
- https://docs.appian.com/suite/help/26.4/Appian_Functions.html
