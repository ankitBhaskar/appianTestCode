# AI Review Guardrails for Appian

## Table of contents

1. [Purpose](#purpose)
2. [How to use this file](#how-to-use-this-file)
3. [Core AI review principles](#core-ai-review-principles)
4. [Pre-generation checks](#pre-generation-checks)
5. [Appian documentation verification](#appian-documentation-verification)
6. [SAIL safety guardrails](#sail-safety-guardrails)
7. [Function safety guardrails](#function-safety-guardrails)
8. [Record type and query guardrails](#record-type-and-query-guardrails)
9. [Database and data model guardrails](#database-and-data-model-guardrails)
10. [Process model guardrails](#process-model-guardrails)
11. [Integration guardrails](#integration-guardrails)
12. [Security guardrails](#security-guardrails)
13. [Testing guardrails](#testing-guardrails)
14. [Prompt patterns](#prompt-patterns)
15. [Review checklist](#review-checklist)
16. [Common AI-generated mistakes](#common-ai-generated-mistakes)
17. [References](#references)

## Purpose

This document defines APN guardrails for reviewing AI-generated Appian specifications, SAIL interfaces, expression rules, process model designs, database scripts and integration patterns.

The aim is to stop common AI-generated issues before they reach build, including invented functions, unsupported component parameters, unsafe save patterns, missing security, weak data modelling and incomplete test coverage.

This is a recommended house standard. Official Appian documentation remains the source of truth.

## How to use this file

Use this file in three situations:

| Situation | How to use it |
|---|---|
| Before asking AI to generate Appian content | Complete the pre-generation checks so the AI has enough context. |
| While reviewing AI output | Apply each guardrail section relevant to the artefact. |
| Before giving work to a developer | Complete the final review checklist and fix high-risk gaps. |

## Core AI review principles

| Principle | Standard |
|---|---|
| Verify, do not assume | Check exact Appian syntax, parameters and compatibility against official documentation. |
| Use the project kit | AI must use the current project prefix, groups, record types and data model. |
| No invented Appian syntax | Reject any function, component or parameter that cannot be confirmed. |
| Prefer simple Appian patterns | Avoid over-engineered SAIL, process models and expression rules. |
| Keep data model first | Do not let AI start with UI before the records, tables and relationships are clear. |
| Make security explicit | UI hiding is not enough. Record, object, process and API security must be documented. |
| Include tests | AI output should include unit tests and end-to-end test scenarios. |

## Pre-generation checks

Before asking AI to generate a technical specification, provide this context.

| Required input | Example |
|---|---|
| Application prefix | `APN` |
| Feature name | Claim assessment, employer service request, payment review |
| User stories | Story ID, role, action, outcome and acceptance criteria |
| Existing record types | `APN_REC_Claim`, `APN_REC_Customer` |
| Existing tables | `apn_claim`, `apn_customer` |
| Groups | `APN_GRP_Admins`, `APN_GRP_CaseManagers` |
| Integration contracts | Request and response payloads where applicable |
| Known constraints | Portal-safe only, mobile-friendly, no new table, read-only, etc. |
| Out of scope | Clearly state what must not be generated |

Recommended instruction:

```text
Use the APN Appian design engine. Do not invent Appian functions,
component parameters, record fields, groups or database columns.
Where official Appian behaviour matters, state the documentation source
or mark the item as requiring verification.
```

## Appian documentation verification

AI-generated Appian content must be checked against official documentation when it includes:

- SAIL components
- Appian functions
- component parameters
- function parameters
- compatibility flags
- record type capabilities
- process model capabilities
- integration behaviour
- portal compatibility
- platform limits

Verification rule:

```text
If the reviewer cannot confirm the Appian object, function, component,
parameter or allowed value, it must not be treated as build-ready.
```

## SAIL safety guardrails

### Required checks

| Check | Required standard |
|---|---|
| Rule inputs | Interfaces use `ri!`, not `pv!`. |
| Working copy | Editable forms use local working copies and write back on submit. |
| Local variables | Use clear top-level local variables for shared state. |
| Null safety | Optional values are guarded before comparison, formatting or indexing. |
| Required fields | Required inputs are marked and business validations are documented. |
| Conditional sections | Use safe conditional logic for groups of components. |
| Refresh behaviour | Expensive queries and integrations do not refresh unnecessarily. |
| Mobile compatibility | Layouts avoid unnecessary wide fixed structures. |
| Accessibility | Labels, instructions and error messages are clear. |

### Safe form pattern

```appian
a!localVariables(
  local!workingRecord: ri!record,
  local!isReadOnly: a!defaultValue(ri!isReadOnly, false),

  a!formLayout(
    titleBar: a!headerTemplateSimple(
      title: "Create Record",
      secondaryText: "Capture the required details"
    ),
    contents: {
      a!textField(
        label: "Reference",
        value: local!workingRecord.reference,
        saveInto: local!workingRecord.reference,
        required: true,
        readOnly: local!isReadOnly
      )
    },
    buttons: a!buttonLayout(
      primaryButtons: {
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
      },
      secondaryButtons: {
        a!buttonWidget(
          label: "Cancel",
          style: "OUTLINE",
          submit: true,
          saveInto: a!save(ri!isCancelled, true),
          validate: false
        )
      }
    )
  )
)
```

## Function safety guardrails

AI must not invent functions or parameters.

| Function area | Review focus |
|---|---|
| Query functions | Confirm fields, filters, paging and total count behaviour. |
| Array functions | Confirm empty-list and null behaviour. |
| Conversion functions | Confirm output type and null handling. |
| Date and time functions | Confirm formatting, timezone assumptions and null handling. |
| Text functions | Confirm string handling and localisation impact. |
| People functions | Confirm they are not used as the only security control. |
| Integration functions | Confirm response shape before indexing. |

Function review prompt:

```text
List every Appian function used in this output. For each function,
state whether it exists in Appian 26.4, whether the parameters are valid,
and whether null handling is safe. Mark anything uncertain as requiring
verification against official Appian documentation.
```

## Record type and query guardrails

### Query standards

AI-generated query rules must:

- use `a!queryRecordType()` for record data where suitable
- specify fields that are read by the caller
- include appropriate paging
- keep sorting inside paging configuration where applicable
- null-guard filter inputs
- avoid query-in-loop patterns
- filter out soft-deleted rows where the model uses soft delete
- set total count only where the UI needs it

Safe query pattern:

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
        batchSize: 50
      )
    ).data
  )
)
```

### Record type review

Every record type specification should include:

| Item | Required |
|---|---|
| Record type name | Yes |
| Description | Yes |
| Source table or source system | Yes |
| Primary key field | Yes |
| Display field | Yes |
| Field mapping | Yes |
| Relationships | Yes |
| Record actions | Where applicable |
| Record views | Where applicable |
| Security | Yes |
| Unit tests | Yes |

## Database and data model guardrails

AI-generated database designs must be reviewed for:

| Area | Guardrail |
|---|---|
| Table names | Use the project prefix and approved naming standard. |
| Primary keys | Avoid bare `id`; use entity-specific keys such as `claim_id`. |
| Foreign keys | Document parent-child relationships clearly. |
| Reference data | Do not use uncontrolled free text for governed statuses. |
| Audit fields | Include created and updated audit fields. |
| Soft delete | Define whether soft delete is required. |
| Versioning | Consider optimistic locking where concurrent edits are possible. |
| Indexing | Index fields used in filters, joins and date range searches. |
| Migration | Include versioned scripts and rollback guidance. |

Database review prompt:

```text
Review this Appian database design for table naming, primary keys,
foreign keys, audit fields, soft delete, reference data, record type
mapping, indexes and migration safety. Return issues as a severity table.
```

## Process model guardrails

AI-generated process model designs must include:

| Area | Required detail |
|---|---|
| Trigger | Record action, related action, start process, timer or integration. |
| Process variables | Name, type, parameter flag and default. |
| Start form mapping | Which PVs feed which RIs. |
| Cancel path | Explicit `isCancelled` handling where relevant. |
| Write Records | What is written and in what sequence. |
| Audit fields | Where they are stamped. |
| Alerts | Support group or admin group. |
| Data management | Archive or deletion approach. |
| Exception handling | Retry, manual review or support alert. |
| Tests | Happy path, cancel path and failure path. |

Process review prompt:

```text
Review this Appian process model design. Confirm the trigger, process
variables, form mappings, write sequence, audit stamping, cancellation,
exception handling, alerts and unit tests. Identify any missing build detail.
```

## Integration guardrails

Before AI writes integration wrapper rules, the reviewer must provide or request a response sample.

Required integration details:

| Area | Required detail |
|---|---|
| Connected system | Name and purpose. |
| Integration name | APN naming convention. |
| Method | GET, POST, PUT, PATCH or DELETE. |
| POST behaviour | State whether POST reads data or modifies data. |
| Authentication | API key, OAuth, basic, mutual TLS or other approved method. |
| Request contract | Required and optional inputs. |
| Response contract | Actual or representative response. |
| Error handling | User message, logging and retry behaviour. |
| Idempotency | Whether repeated calls are safe. |

Safe response handling pattern:

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

## Security guardrails

AI output must not rely on hiding UI components as the only security control.

Security must be considered at:

- application security
- folder security
- object security
- record type security
- record action visibility
- process model start security
- interface conditional visibility
- web API authentication and authorisation
- connected system credential security
- database access patterns

Security review prompt:

```text
Review this Appian design for security. Confirm group model, object security,
record type security, record action visibility, process start security,
web API access, connected system credentials and production support access.
Identify any place where UI visibility is being used as the only control.
```

## Testing guardrails

Every AI-generated specification must include tests by layer.

| Layer | Required tests |
|---|---|
| Database | DDL, FK, seed data and migration checks. |
| Record type | Sync, relationships, security and record views. |
| Expression rule | Null input, happy path, empty result and boundary cases. |
| Interface | Load, validation, submit, cancel, read-only and empty state. |
| Process model | Start, cancel, write, exception and alert paths. |
| Integration | Success, error, timeout, malformed response and retry scenarios. |
| Security | Authorised and unauthorised user checks. |
| End-to-end | Realistic business scenario from start to finish. |

## Prompt patterns

### Generate a technical specification

```text
Create an Appian technical specification using the APN design engine.
Use the supplied project kit, data model and user stories only.
Do not invent record fields, groups, Appian functions or SAIL parameters.
Include DDL, record types, expression rules, interfaces, process models,
actions, security, tests, deployment notes and rollback considerations.
Mark anything that requires official Appian documentation verification.
```

### Review SAIL code

```text
Review this SAIL code for Appian 26.4 compatibility. Check for invented
components, unsupported parameters, unsafe saveInto patterns, null handling,
query-in-loop patterns, refresh behaviour, accessibility and mobile usability.
Return a table with severity, issue, reason and corrected pattern.
```

### Review database design

```text
Review this database design for Appian record mapping. Check table naming,
primary keys, foreign keys, audit fields, soft delete, versioning, indexes,
reference data, record type relationships and migration safety.
```

### Review a full AI-generated specification

```text
Review this specification against the APN Appian design engine. Identify
unsupported Appian syntax, missing build detail, unclear assumptions,
security gaps, testing gaps, deployment gaps and places where official
Appian documentation verification is required.
```

## Review checklist

| Review area | Pass or fail |
|---|---|
| Uses current project prefix. |  |
| Does not invent Appian objects, functions or parameters. |  |
| Links unusual or important Appian features to official documentation. |  |
| Data model is clear before UI design. |  |
| Record types include relationships and security. |  |
| Expression rules are null-safe. |  |
| Query rules avoid query-in-loop behaviour. |  |
| Interfaces use safe working-copy save patterns. |  |
| Refresh behaviour is intentional. |  |
| Process models include variables, mappings and write sequence. |  |
| Integrations include request and response contracts. |  |
| Security is enforced beyond UI hiding. |  |
| Tests exist at each layer. |  |
| Deployment and rollback are considered. |  |

## Common AI-generated mistakes

| Mistake | Correction |
|---|---|
| Inventing SAIL parameters | Verify against official component reference. |
| Inventing Appian functions | Verify against official function reference. |
| Using `pv!` in interfaces | Use `ri!` in interfaces. |
| Using `ri!` in process model notes | Use `pv!` in process models. |
| Missing `fields` in record queries | Specify every field the caller reads. |
| Querying inside `a!forEach()` | Query once, then transform in memory. |
| Unsafe dictionary access | Use `index()` with a safe default. |
| Formatting null dates | Check `isnull()` before formatting. |
| Saving directly into complex record references without a working copy | Save into local working copy and write back on submit. |
| Starting with UI before data design | Design database and records first. |
| Treating UI hiding as security | Add object, record, process or API security. |
| Missing integration response sample | Request actual or representative response payload first. |
| No tests | Add unit and end-to-end tests by layer. |

## References

Official Appian 26.4 documentation remains the source of truth:

- https://docs.appian.com/suite/help/26.4/
- https://docs.appian.com/suite/help/26.4/Appian_Functions.html
- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/SAIL_Recipes.html
- https://docs.appian.com/suite/help/26.4/interface-performance.html
- https://docs.appian.com/suite/help/26.4/refresh-behavior-interfaces.html
- https://docs.appian.com/suite/help/26.4/Records.html
- https://docs.appian.com/suite/help/26.4/Process_Modeling.html
- https://docs.appian.com/suite/help/26.4/Integration_Object.html
- https://docs.appian.com/suite/help/26.4/Web_APIs.html
