# 04 Process Model and Action Standards

## Table of contents

1. [Purpose](#purpose)
2. [Process design principles](#process-design-principles)
3. [Start form pattern](#start-form-pattern)
4. [Process variables](#process-variables)
5. [Record actions and related actions](#record-actions-and-related-actions)
6. [Write Records pattern](#write-records-pattern)
7. [Audit stamping](#audit-stamping)
8. [Exception handling](#exception-handling)
9. [Process model test checklist](#process-model-test-checklist)
10. [References](#references)

## Purpose

This file defines APN process model and action standards for Appian projects.

## Process design principles

| Principle | Standard |
|---|---|
| Keep process models thin | Use process models for orchestration, not complex expression logic. |
| Use clear process variables | Process variables should represent business state, not temporary UI fields. |
| Prefer start forms for simple actions | Use start forms for create and edit flows unless pre-form process logic is required. |
| Avoid unnecessary script tasks | Query and validation logic should usually live in expression rules or interfaces. |
| Make cancellation explicit | Use an `isCancelled` variable and an XOR gateway where required. |
| Configure alerts | Every process model should have an alert group. |

## Start form pattern

Recommended flow:

```text
Start Form
  -> XOR isCancelled?
    -> Yes: End Cancelled
    -> No: Write Records
      -> End Complete
```

Use a user input task instead of a start form only when the process must run logic before the first user screen.

## Process variables

Recommended process variable table in specifications:

| Name | Type | Parameter | Default | Purpose |
|---|---|---|---|---|
| `parentRecord` | Record Type | Yes | None | Existing parent context. |
| `record` | Record Type | Yes or No | Blank record | Record being created or updated. |
| `isCancelled` | Boolean | No | `false` | Controls cancel path. |
| `documents` | Document[] | No | `{}` | Uploaded documents. |

If a start form needs a value passed from a record action, mark the matching process variable as a parameter.

## Record actions and related actions

| Action type | When to use | Context |
|---|---|---|
| Record action | Create a new record from a record list or action entry point. | Usually no existing record context. |
| Related action | Act on an existing record or create a child from a parent record. | Uses `rv!record` or `rv!identifier`. |

Specification block standard:

```text
Action Type:    Related Action
Configured On:  APN_REC_Customer
Name:           Create Claim
Description:    Opens a form to create a claim for the selected customer.
Process Model:  APN_PM_CreateClaim
Input Mapping:  pv!parentRecord = rv!record
Dialog Size:    MEDIUM
Visibility:     APN_GRP_CaseManagers, APN_GRP_Admins
Icon:           plus-circle
```

## Write Records pattern

Use one Write Records node where records can be written together safely.

Use separate Write Records nodes when:

- a child record needs a generated parent primary key
- writes must occur in a strict audit sequence
- error handling differs by write step

## Audit stamping

For form-driven writes, stamp user-facing audit fields in the submit button `saveInto` where practical.

In interfaces:

```appian
a!save(local!workingRecord.updatedBy, loggedInUser()),
a!save(local!workingRecord.updatedOn, now())
```

In process models, use `pp!initiator` where process context is required.

## Exception handling

Every process specification should state:

| Area | Required detail |
|---|---|
| Alert group | Which group receives alerts. |
| Retryable failures | Which failures can be retried. |
| Non-retryable failures | Which failures need manual review. |
| Support visibility | Where support users can find failed items. |
| User message | What the user sees if the action fails. |

## Process model test checklist

| Test | Pass or fail |
|---|---|
| Start form receives expected parameters. |  |
| Cancel path ends without writing records. |  |
| Happy path writes expected records. |  |
| Generated primary keys are available before child writes. |  |
| Audit fields are populated. |  |
| Process display name is meaningful. |  |
| Alerts are configured. |  |
| Data management/archive setting is configured. |  |
| End node has expected completion behaviour. |  |

## References

Official Appian 26.4 documentation remains the source of truth:

- https://docs.appian.com/suite/help/26.4/Process_Modeling.html
- https://docs.appian.com/suite/help/26.4/Records.html
- https://docs.appian.com/suite/help/26.4/record-actions.html
