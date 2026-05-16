# Appian 26.4 Functions Engineering Reference

## Table of contents

1. [Purpose](#purpose)
2. [Source of truth](#source-of-truth)
3. [AI generation rule](#ai-generation-rule)
4. [Function category map](#function-category-map)
5. [Modern expression state functions](#modern-expression-state-functions)
6. [Looping and list handling](#looping-and-list-handling)
7. [Safe access and dictionary handling](#safe-access-and-dictionary-handling)
8. [Paging and sorting functions](#paging-and-sorting-functions)
9. [Record query functions](#record-query-functions)
10. [Query helper functions](#query-helper-functions)
11. [Conversion and formatting functions](#conversion-and-formatting-functions)
12. [Logical functions](#logical-functions)
13. [Integration response handling](#integration-response-handling)
14. [Old-version function handling](#old-version-function-handling)
15. [Common AI mistakes](#common-ai-mistakes)
16. [Function review checklist](#function-review-checklist)
17. [References](#references)

## Purpose

This document is a practical Appian 26.4 function guide for APN engineering teams and AI-assisted Appian development.

It focuses on functions that are frequently used, frequently misused, or commonly invented incorrectly by AI tools.

It is not a verbatim replacement for the full Appian function reference. The official Appian 26.4 documentation remains the source of truth for exact signatures, parameters, return types, examples, compatibility notes and legacy function versions.

## Source of truth

Use these official Appian pages when exact syntax matters:

- https://docs.appian.com/suite/help/26.4/Appian_Functions.html
- https://docs.appian.com/suite/help/26.4/function_versions.html
- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/SAIL_Recipes.html

## AI generation rule

AI-generated Appian code must not invent functions, parameters, allowed values or helper syntax.

```text
Use Appian Expression Language and SAIL only.
Do not use Java, JavaScript, React, CSS, HTML, Python or pseudo-code inside Appian expressions.
If a function cannot be confirmed in official Appian documentation, do not use it in build-ready code.
```

## Function category map

The official Appian function reference organises functions into categories. Use the categories below to review whether a function is being used in the right context.

| Category | Typical use | Review focus |
|---|---|---|
| Array | Work with lists and arrays. | Type consistency, empty lists and indexing safety. |
| Base Conversion | Convert between number bases. | Confirm genuine business need. |
| Connector | Work with connected systems and integration outcomes. | Response shape and error handling. |
| Conversion | Convert between text, number, date and typed values. | Output type and null behaviour. |
| Custom Fields | Support custom record field expressions. | Performance and record compatibility. |
| Date and Time | Date calculations and formatting. | Null safety, timezone and display format. |
| Evaluation | Control expression evaluation and local state. | Refresh and repeated evaluation. |
| Informational | Inspect values, types and system information. | Avoid using diagnostics as business logic shortcuts. |
| Logical | Conditional expressions. | Null-safe branching and readable logic. |
| Looping | Iterate through lists. | Avoid queries or integrations inside loops. |
| Mathematical | Numeric calculations. | Divide-by-zero and decimal precision. |
| People | Users, groups and people data. | Security and display name handling. |
| Scripting | Script support and specialised logic. | Confirm correct context. |
| Smart Service | Smart service related functionality. | Confirm process context and side effects. |
| Statistical | Aggregations and statistical calculations. | Empty-list behaviour. |
| System | System and environment information. | Avoid environment-specific assumptions. |
| Text | String manipulation and formatting. | Null safety and translation impact. |
| Trigonometry | Mathematical trigonometric calculations. | Confirm units and business need. |

## Modern expression state functions

### `a!localVariables()`

Recommended standard:

- use for modern expression and interface state
- prefer it over legacy expression-state approaches in new development
- declare shared locals at the top of the expression or interface
- null-guard dependent locals
- avoid scattering unrelated nested local variable blocks across a large interface

Pattern:

```appian
a!localVariables(
  local!workingRecord: ri!record,
  local!isReadOnly: a!defaultValue(ri!isReadOnly, false),
  local!referenceData: rule!APN_QRY_GetActiveReferenceData(),

  /* return the expression body */
  local!workingRecord
)
```

Review checks:

| Check | Required outcome |
|---|---|
| Are locals named clearly? | Yes. |
| Are optional inputs guarded? | Yes. |
| Are expensive queries repeated unnecessarily? | No. |
| Is a local used outside its scope? | No. |

### `a!refreshVariable()`

Recommended standard:

- use only when refresh behaviour is deliberate
- avoid refreshing expensive queries on every keystroke
- refresh search results after explicit filter changes or Search actions
- refresh dependent dropdown values only when the parent value changes

Pattern:

```appian
local!selectedStatus,
local!claims: a!refreshVariable(
  value: rule!APN_QRY_SearchClaims(status: local!selectedStatus),
  refreshOnVarChange: local!selectedStatus
)
```

### `a!save()`

Recommended standard:

- use inside `saveInto`
- stamp audit fields during submit where appropriate
- save form changes into a local working copy during editing
- write the local working copy back to `ri!record` on submit

Pattern:

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

## Looping and list handling

### `a!forEach()`

Use `a!forEach()` to transform already-loaded data.

Common function variables used in review:

| Function variable | Purpose |
|---|---|
| `fv!item` | Current item. |
| `fv!index` | Current index. |
| `fv!identifier` | Identifier where available. |
| `fv!isFirst` | Whether current item is first. |
| `fv!isLast` | Whether current item is last. |
| `fv!itemCount` | Number of items. |

Safe pattern:

```appian
a!forEach(
  items: local!claims,
  expression: a!map(
    claimReference: fv!item[recordType!APN_REC_Claim.fields.claimReference],
    status: fv!item[recordType!APN_REC_Claim.fields.claimStatusCode]
  )
)
```

Avoid query-in-loop patterns:

```appian
/* Avoid unless specifically reviewed for tiny datasets */
a!forEach(
  items: local!claimIds,
  expression: rule!APN_QRY_GetClaimById(claimId: fv!item)
)
```

### `wherecontains()`

Review standard:

- ensure search values and target array are the same type
- expect an empty list when no match is found
- avoid mixing text and number values without explicit conversion

### `length()`

Review standard:

- confirm whether the input can be null
- use safe defaults where needed
- avoid using count logic as a security control

### `joinarray()`

Review standard:

- use for display strings only where appropriate
- do not use it to construct SQL or integration payloads unsafely
- null-guard optional list items before display

## Safe access and dictionary handling

### `index()`

Use `index()` for safe access to lists, maps, dictionaries, CDTs and record-shaped data where appropriate.

Pattern:

```appian
index(local!response, "body", {})
```

Nested pattern:

```appian
local!body: index(local!response, "body", {}),
local!records: index(local!body, "records", {})
```

Review checks:

| Check | Required outcome |
|---|---|
| Is there a safe default? | Yes. |
| Is the expected type clear? | Yes. |
| Is dictionary key spelling based on a real payload? | Yes. |
| Is the code assuming a typed record when the response is a dictionary? | No. |

### `a!map()`

Use `a!map()` for simple structured values returned from expression rules or used as interface state.

Pattern:

```appian
a!map(
  externalId: tostring(index(fv!item, "id", null)),
  displayName: tostring(index(fv!item, "displayName", ""))
)
```

## Paging and sorting functions

### `a!pagingInfo()`

Review standard:

- use explicit `startIndex`
- use appropriate `batchSize`
- include sorting inside paging where required
- do not load all rows unless explicitly justified

Pattern:

```appian
a!pagingInfo(
  startIndex: 1,
  batchSize: 50,
  sort: a!sortInfo(
    field: recordType!APN_REC_Claim.fields.createdOn,
    ascending: false
  )
)
```

### `a!sortInfo()`

Review standard:

- sort field must exist and be queryable
- keep `a!sortInfo()` in the correct context
- use sort fields aligned to database indexes where large datasets are involved

## Record query functions

### `a!queryRecordType()`

Recommended for modern record-backed queries.

Critical review rule:

```text
Specify every field that the caller reads. If fields are omitted or empty, do not assume all fields are returned.
```

Safe pattern:

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
        recordType!APN_REC_Claim.fields.claimStatusCode,
        recordType!APN_REC_Claim.fields.createdOn
      },
      filters: {
        a!queryFilter(
          field: recordType!APN_REC_Claim.fields.customerId,
          operator: "=",
          value: local!customerId
        ),
        a!queryFilter(
          field: recordType!APN_REC_Claim.fields.isDeleted,
          operator: "=",
          value: false
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

### `a!queryRecordByIdentifier()`

Use when retrieving a single record by identifier where that pattern is supported and appropriate.

Review checks:

| Check | Required outcome |
|---|---|
| Identifier is null-guarded. | Yes. |
| Returned record shape is understood. | Yes. |
| Missing record behaviour is handled. | Yes. |

### `a!queryEntity()`

Use only where the application architecture still requires data store entity querying.

Review standard:

- prefer record type queries for modern record-backed Appian design where suitable
- do not mix data store entity and record type patterns unnecessarily
- ensure paging, filters and return shapes are handled safely

## Query helper functions

### `a!queryFilter()`

Review standard:

- field must be a valid field reference
- operator must be valid for the data type and Appian query function
- value must be null-guarded for equality filters
- value type must match field type

Pattern:

```appian
a!queryFilter(
  field: recordType!APN_REC_Claim.fields.claimStatusCode,
  operator: "=",
  value: local!selectedStatus
)
```

### `a!queryLogicalExpression()`

Use to combine filter groups where simple filter lists are not expressive enough.

Review standard:

- keep logical nesting readable
- avoid building complex dynamic filter trees without tests
- ensure null filters are ignored or avoided safely

### `a!relatedRecordData()`

Use for related record data where supported by record query patterns.

Review standard:

- confirm relationship name exists
- confirm fields and limits are deliberate
- avoid pulling large related datasets into simple screens

### `a!selectionFields()` and `a!aggregationFields()`

Use where record query selections or aggregations require them.

Review standard:

- confirm exact syntax in official Appian documentation
- confirm return shape before using the result in grids or charts
- add tests for empty datasets and grouped results

## Conversion and formatting functions

Review standards:

| Scenario | Standard |
|---|---|
| Plain value to text | Use the documented conversion function suitable for text output. |
| Date display | Null-check before formatting. |
| Number display | Confirm decimal precision and null handling. |
| Dictionary value to typed value | Use safe access and explicit conversion. |
| User-facing values | Do not expose raw codes unless intended. |

Date display pattern:

```appian
if(
  isnull(local!createdOn),
  "-",
  text(local!createdOn, "dd/MM/yyyy")
)
```

## Logical functions

Use Appian logical functions and supported operators. Do not use JavaScript logical operators.

Correct:

```appian
and(
  not(isnull(local!claimStatus)),
  local!claimStatus = "OPEN"
)
```

Reject:

```text
local!claimStatus !== null && local!claimStatus === "OPEN"
```

## Integration response handling

Treat integration responses as dictionaries unless the implementation explicitly maps them differently.

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

Mandatory rule:

```text
Do not write integration indexing logic until an actual or representative response payload has been reviewed.
```

## Old-version function handling

When old Appian applications contain suffixed function variants, do not refactor blindly.

Review standard:

- identify old-version function suffixes
- check the official Function and Component Versions page
- confirm behaviour changes before replacing old signatures
- add regression tests before and after refactoring

## Common AI mistakes

| Mistake | Correction |
|---|---|
| Inventing a plausible Appian function | Confirm in official Appian function reference. |
| Using JavaScript array methods such as `.map()` | Use Appian list and looping functions. |
| Using `&&`, `||`, `!` | Use Appian logical functions and supported operators. |
| Missing `fields` in record queries | Specify fields read by the caller. |
| Querying inside loops | Query once, then transform. |
| Unsafe `index()` usage without defaults | Provide safe defaults. |
| Assuming integration response is typed | Treat response as dictionary unless mapped. |
| Formatting null dates | Check null first. |
| Treating old-version functions as automatically safe to replace | Verify version-specific behaviour first. |

## Function review checklist

| Check | Pass or fail |
|---|---|
| Every function exists in official Appian 26.4 documentation. |  |
| No JavaScript, Java, Python or pseudo-function syntax is present. |  |
| Every named parameter is valid for that function. |  |
| Query functions include required fields. |  |
| Query filters have matching value types. |  |
| Equality filters are null-guarded. |  |
| Sort and paging are in the correct place. |  |
| List access uses safe defaults. |  |
| Integration dictionary access uses real response keys. |  |
| Date and number formatting is null-safe. |  |
| Old-version functions have been checked before refactoring. |  |

## References

Official Appian documentation:

- https://docs.appian.com/suite/help/26.4/Appian_Functions.html
- https://docs.appian.com/suite/help/26.4/function_versions.html
- https://docs.appian.com/suite/help/26.4/Records.html
- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/SAIL_Recipes.html
- https://docs.appian.com/suite/help/26.4/interface-performance.html
- https://docs.appian.com/suite/help/26.4/refresh-behavior-interfaces.html
