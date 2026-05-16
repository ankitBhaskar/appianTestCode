# 03 SAIL Interface Standards

## Table of contents

1. [Purpose](#purpose)
2. [Interface design principles](#interface-design-principles)
3. [Rule inputs and local variables](#rule-inputs-and-local-variables)
4. [Save behaviour](#save-behaviour)
5. [Null safety](#null-safety)
6. [Querying from interfaces](#querying-from-interfaces)
7. [Grid standards](#grid-standards)
8. [Component guardrails](#component-guardrails)
9. [User display standards](#user-display-standards)
10. [Interface test checklist](#interface-test-checklist)
11. [References](#references)

## Purpose

This file defines APN SAIL interface standards for Appian 26.4 projects.

Use it to reduce common AI-generated SAIL mistakes, especially invalid parameters, weak null handling, unsafe saveInto patterns and inconsistent local variable scoping.

## Interface design principles

| Principle | Standard |
|---|---|
| One clear purpose | Each interface should have one main job. |
| Data before layout | Understand record inputs and save behaviour before visual design. |
| Readability | Use clear sections, meaningful local variables and simple expressions. |
| Null safe by default | Guard all optional inputs, query results and date formatting. |
| Reviewable | Full SAIL examples should be copy-pasteable and not pseudocode. |

## Rule inputs and local variables

Interfaces use `ri!`. Process models use `pv!`.

Do not reference `pv!` inside an interface.

Recommended rule input pattern:

```text
ri!parentRecord     Record Type      Input
ri!record           Record Type      Input/Output
ri!isCancelled      Boolean          Output
ri!documents        Document[]       Output
```

Use one top-level `a!localVariables()` for interface state.

```appian
a!localVariables(
  local!workingRecord: ri!record,
  local!isReadOnly: a!defaultValue(ri!isReadOnly, false),
  local!statusOptions: rule!APN_QRY_GetActiveClaimStatuses(),

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

Recommended pattern:

```appian
a!textField(
  label: "Claim Reference",
  value: local!workingRecord.claimReference,
  saveInto: local!workingRecord.claimReference,
  required: true
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

Prefer this:

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

## Grid standards

Grid rules:

- use explicit fields in query rules feeding the grid
- use `pagingInfo` deliberately
- avoid query-in-loop patterns
- use null-safe formatting for dates and numbers
- avoid unsupported grid parameters
- keep actions in a clear actions column

Example grid column pattern:

```appian
a!gridColumn(
  label: "Claim Reference",
  value: fv!row[recordType!APN_REC_Claim.fields.claimReference],
  width: "MEDIUM"
)
```

## Component guardrails

| Area | Standard |
|---|---|
| Form title | Use `titleBar`, not unsupported label parameters. |
| Conditional sections | Use `if()` for groups of components. Use `showWhen` only on supported individual components. |
| Dropdown placeholder | Use `placeholder`. |
| Buttons | Use documented button styles only. |
| Record actions | Use record action components rather than forcing actions into buttons. |
| String special characters | Use `char()` where required. |
| Local variables | Do not scatter unrelated `a!localVariables()` blocks throughout the interface. |

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
| Grid renders empty state correctly. |  |
| Usernames render with display rule. |  |

## References

Official Appian 26.4 documentation remains the source of truth:

- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/Appian_Functions.html
- https://docs.appian.com/suite/help/26.4/recipe_display_data_in_a_grid.html
