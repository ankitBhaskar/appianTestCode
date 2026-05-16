# Appian 26.4 Functions Usage Guide

## Table of contents

1. [Purpose](#purpose)
2. [Important accuracy rule](#important-accuracy-rule)
3. [Function category catalogue](#function-category-catalogue)
4. [How to document a function in a technical spec](#how-to-document-a-function-in-a-technical-spec)
5. [Common SAIL and expression function patterns](#common-sail-and-expression-function-patterns)
6. [Query function standards](#query-function-standards)
7. [Looping standards](#looping-standards)
8. [Null and type safety standards](#null-and-type-safety-standards)
9. [Date and time standards](#date-and-time-standards)
10. [Text standards](#text-standards)
11. [Logical standards](#logical-standards)
12. [Integration response handling](#integration-response-handling)
13. [Function review checklist](#function-review-checklist)
14. [References](#references)

## Purpose

This guide defines APN standards for using Appian functions safely in expressions, SAIL interfaces, record queries, integrations and process-backed forms.

It is designed to prevent AI-generated Appian mistakes such as invented functions, wrong parameter names, unsafe null handling, query-in-loop patterns and incorrect assumptions about return types.

## Important accuracy rule

This file does not attempt to reproduce every Appian function signature. The official Appian 26.4 function documentation must be used for exact syntax, parameters, return types, examples, compatibility notes and version changes.

Engineering rule:

```text
When writing a specification, include the official documentation URL for every function family or unusual function used.
Do not invent parameters or return types.
Where exact syntax matters, verify in the official Appian 26.4 documentation.
```

## Function category catalogue

The official Appian function reference groups functions into categories. The APN review standard is to classify every non-trivial function by category so reviewers can spot inappropriate use.

| Category | Typical use in Appian engineering | Review focus |
|---|---|---|
| Array | List handling, flattening, merging, indexing and membership checks. | Null handling, empty-list behaviour and type consistency. |
| Base Conversion | Converting values across number bases where required. | Confirm genuine need; avoid unnecessary complexity. |
| Connector | Calling connected systems and integrations. | Authentication, response handling, errors and dictionary access. |
| Conversion | Converting text, numbers, dates and typed values. | Avoid unsafe casts and confirm return types. |
| Custom Fields | Custom record field logic. | Confirm record type support and performance impact. |
| Date and Time | Date calculations, formatting and comparison. | Null-safe date handling and timezone assumptions. |
| Evaluation | Controlling expression evaluation. | Avoid hidden expensive evaluation and accidental repeated execution. |
| Informational | Inspecting type, environment or value properties. | Use for validation and diagnostics, not business shortcuts. |
| Logical | `if`, `and`, `or`, `not` and condition logic. | Null-safe comparisons and readable branching. |
| Looping | `a!forEach()` and repeated transformations. | Avoid queries and integrations inside loops. |
| Mathematical | Numeric calculations. | Type safety and divide-by-zero handling. |
| People | Users, groups and membership checks. | Security, group governance and display names. |
| Scripting | Script or process-support functions. | Use only in appropriate context. |
| Smart Service | Functions associated with Appian services. | Confirm process context and side effects. |
| Statistical | Aggregation and statistical operations. | Correct data type and empty-list handling. |
| System | System metadata or environment behaviour. | Avoid environment-specific assumptions. |
| Text | String manipulation and formatting. | Null safety and localisation. |
| Trigonometry | Mathematical calculations. | Confirm business need and expected units. |

## How to document a function in a technical spec

Use this block for important or unusual functions.

```text
Function:          a!queryRecordType()
Category:          Record query / Appian function
Official URL:      https://docs.appian.com/suite/help/26.4/Appian_Functions.html
Purpose:           Query claim records for the current customer.
Inputs used:       recordType, fields, filters, pagingInfo
Return handling:   Reads `.data`; sets `fetchTotalCount: true` only where total count is required.
Null safety:       customerId is null-guarded before query execution.
Performance note:  Selects only required fields and avoids query-in-loop usage.
```

## Common SAIL and expression function patterns

### Local variable pattern

```appian
a!localVariables(
  local!safeId: ri!recordId,
  local!results: if(
    isnull(local!safeId),
    {},
    rule!APN_QRY_GetRecordsByParent(parentId: local!safeId)
  ),
  local!results
)
```

### Safe default pattern

```appian
a!defaultValue(ri!isReadOnly, false)
```

### Safe list access pattern

```appian
index(local!records, 1, null)
```

### Safe dictionary access pattern

```appian
index(local!response, "body", null)
```

## Query function standards

When using record query functions:

- specify the record type
- specify fields that will be read by the caller
- apply filters deliberately
- keep sort information inside paging information where required
- use paging for grids and search results
- set total count only when the UI needs it
- null-guard filter values
- avoid calling query rules inside `a!forEach()` unless the dataset is intentionally tiny and reviewed

Recommended pattern:

```appian
a!localVariables(
  local!customerId: ri!customerId,
  if(
    isnull(local!customerId),
    {},
    a!queryRecordType(
      recordType: recordType!APN_REC_Claim,
      fields: {
        recordType!APN_REC_Claim.fields.claimId,
        recordType!APN_REC_Claim.fields.claimReference,
        recordType!APN_REC_Claim.fields.claimStatusCode
      },
      filters: {
        a!queryFilter(
          field: recordType!APN_REC_Claim.fields.customerId,
          operator: "=",
          value: local!customerId
        )
      },
      pagingInfo: a!pagingInfo(
        startIndex: 1,
        batchSize: 50,
        sort: a!sortInfo(
          field: recordType!APN_REC_Claim.fields.createdOn,
          ascending: false
        )
      )
    ).data
  )
)
```

## Looping standards

Use looping functions to transform already-loaded data, not to run repeated database queries or integration calls.

Preferred:

```appian
a!forEach(
  items: local!claims,
  expression: a!map(
    claimReference: fv!item[recordType!APN_REC_Claim.fields.claimReference],
    status: fv!item[recordType!APN_REC_Claim.fields.claimStatusCode]
  )
)
```

Avoid:

```appian
a!forEach(
  items: local!claimIds,
  expression: rule!APN_QRY_GetClaimById(claimId: fv!item)
)
```

## Null and type safety standards

| Scenario | Standard |
|---|---|
| Optional Boolean | Use `a!defaultValue(value, false)`. |
| Optional Text | Use `a!defaultValue(value, "")` when safe for display. |
| Optional Date | Check `isnull()` before formatting. |
| Dictionary access | Use `index(dictionary, key, default)`. |
| List access | Use `index(list, position, default)`. |
| Type conversion | Use documented conversion functions and confirm expected output type. |

## Date and time standards

Date formatting must be null-safe.

```appian
if(
  isnull(local!createdOn),
  "-",
  text(local!createdOn, "dd/MM/yyyy")
)
```

Do not use date formatting functions for plain text conversion. Use a documented conversion function suited to the target type.

## Text standards

For text display:

- null-guard optional values
- avoid building long translated sentences through many concatenated fragments
- use shared formatting rules for repeated display patterns
- use `char()` where special characters are needed and supported

Example:

```appian
concat(
  "Claim ",
  a!defaultValue(local!claimReference, "unknown"),
  " submitted"
)
```

## Logical standards

Keep conditions readable and null-safe.

```appian
and(
  not(isnull(local!claimStatus)),
  local!claimStatus = "OPEN"
)
```

Avoid complicated nested conditions in component parameters. Move complex logic into a local variable or expression rule.

## Integration response handling

Integration responses should be treated as dictionaries unless officially mapped otherwise in the implementation.

Safe pattern:

```appian
a!localVariables(
  local!response: rule!APN_INT_SearchCustomer(criteria: ri!criteria),
  local!body: index(local!response, "body", {}),
  local!records: index(local!body, "records", {}),
  a!forEach(
    items: local!records,
    expression: a!map(
      externalId: tostring(index(fv!item, "id", null)),
      displayName: tostring(index(fv!item, "displayName", ""))
    )
  )
)
```

Before writing this logic, obtain a real or representative response payload.

## Function review checklist

| Check | Pass or fail |
|---|---|
| Every non-trivial function exists in Appian 26.4 documentation. |  |
| No invented parameters are present. |  |
| Query functions specify fields read by the caller. |  |
| Query filter inputs are null-guarded. |  |
| Sort information is placed correctly. |  |
| Loops do not run avoidable queries or integrations. |  |
| Dictionary access uses safe indexing. |  |
| Date formatting is null-safe. |  |
| Type conversion uses the correct function for the target type. |  |
| People/group functions are not used as the only security control. |  |
| Expensive functions are not repeatedly evaluated without need. |  |

## References

Official Appian 26.4 documentation remains the source of truth:

- https://docs.appian.com/suite/help/26.4/Appian_Functions.html
- https://docs.appian.com/suite/help/26.4/function_versions.html
- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/SAIL_Recipes.html
- https://docs.appian.com/suite/help/26.4/interface-performance.html
- https://docs.appian.com/suite/help/26.4/refresh-behavior-interfaces.html
