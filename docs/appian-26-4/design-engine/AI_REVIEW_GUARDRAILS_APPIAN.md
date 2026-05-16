# AI Review Guardrails for Appian

## Table of contents

1. [Purpose](#purpose)
2. [Critical rule: Appian only](#critical-rule-appian-only)
3. [How to use this file](#how-to-use-this-file)
4. [Core AI review principles](#core-ai-review-principles)
5. [Pre-generation checks](#pre-generation-checks)
6. [Appian documentation verification](#appian-documentation-verification)
7. [Language contamination guardrails](#language-contamination-guardrails)
8. [SAIL syntax contract](#sail-syntax-contract)
9. [Component and parameter guardrails](#component-and-parameter-guardrails)
10. [Allowed-value guardrails](#allowed-value-guardrails)
11. [SAIL safety guardrails](#sail-safety-guardrails)
12. [Function safety guardrails](#function-safety-guardrails)
13. [Record type and query guardrails](#record-type-and-query-guardrails)
14. [Database and data model guardrails](#database-and-data-model-guardrails)
15. [Process model guardrails](#process-model-guardrails)
16. [Integration guardrails](#integration-guardrails)
17. [Security guardrails](#security-guardrails)
18. [Testing guardrails](#testing-guardrails)
19. [Prompt patterns](#prompt-patterns)
20. [Mandatory AI self-check before returning code](#mandatory-ai-self-check-before-returning-code)
21. [Review checklist](#review-checklist)
22. [Common AI-generated mistakes](#common-ai-generated-mistakes)
23. [Rejected substitution table](#rejected-substitution-table)
24. [References](#references)

## Purpose

This document defines APN guardrails for reviewing AI-generated Appian specifications, SAIL interfaces, expression rules, process model designs, database scripts and integration patterns.

The aim is to stop common AI-generated issues before they reach build, including invented functions, unsupported component parameters, unsafe save patterns, non-Appian syntax, missing security, weak data modelling and incomplete test coverage.

This is a recommended house standard. Official Appian documentation remains the source of truth.

## Critical rule: Appian only

AI-generated Appian code must use Appian Expression Language and SAIL only.

Do not mix in syntax from Java, JavaScript, TypeScript, React, Angular, HTML, CSS, SQL procedural logic, Python, JSONPath or pseudo-code unless the output section is explicitly a database script, Mermaid diagram, JSON example, HTTP payload or explanatory text.

For SAIL and expression rules:

```text
Only use Appian-supported functions, SAIL components, named parameters,
allowed values, rule input syntax, local variable syntax and record references.
```

If a value, function, parameter or component cannot be confirmed as Appian-supported, it must not be included in build-ready code.

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
| Appian only | SAIL code must not contain Java, JavaScript, React, CSS or made-up pseudo syntax. |
| Verify, do not assume | Check exact Appian syntax, parameters and compatibility against official documentation. |
| Use the project kit | AI must use the current project prefix, groups, record types and data model. |
| No invented Appian syntax | Reject any function, component, parameter or allowed value that cannot be confirmed. |
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
Use the APN Appian design engine. Generate Appian Expression Language
and SAIL only. Do not use Java, JavaScript, React, CSS, HTML-style UI,
Python or pseudo-code inside SAIL examples. Do not invent Appian functions,
component parameters, allowed values, record fields, groups or database
columns. Where official Appian behaviour matters, state the documentation
source or mark the item as requiring verification.
```

## Appian documentation verification

AI-generated Appian content must be checked against official documentation when it includes:

- SAIL components
- Appian functions
- component parameters
- function parameters
- allowed values for parameters
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

## Language contamination guardrails

AI often borrows patterns from other languages. In Appian, that creates immediate evaluation errors.

### Forbidden inside SAIL code

Do not use these patterns inside SAIL or expression rules.

| Foreign pattern | Why it is wrong in SAIL | Appian approach |
|---|---|---|
| JavaScript object syntax, such as `{ key: value }` for arbitrary UI state | Appian has its own expression syntax and map handling. | Use Appian-supported maps, lists and named parameters. |
| React-style components, such as `<Button>` or `<div>` | SAIL is not JSX or HTML. | Use Appian SAIL components such as `a!buttonWidget()` and layout functions. |
| CSS properties, such as `fontWeight: "bold"` | SAIL does not accept arbitrary CSS. | Use Appian component parameters and documented style values. |
| Java methods, such as `.getName()` or `.size()` | Appian expressions do not use Java method calls. | Use Appian functions and record field references. |
| JavaScript operators, such as `===`, `&&`, `||`, `!` | Appian uses its own operators and logical functions. | Use Appian-supported comparison and logical functions such as `and()`, `or()`, `not()`. |
| Python keywords, such as `None`, `True`, `False` | Appian has its own literal values and functions. | Use Appian expression syntax. |
| HTML tags, such as `<b>`, `<strong>`, `<span>` | SAIL rich text is component-based, not HTML. | Use `a!richTextDisplayField()` with `a!richTextItem()`. |
| CSS colour classes, such as `text-red-500` | SAIL does not use Tailwind or CSS classes. | Use Appian-supported colour inputs where available. |
| JSONPath, such as `response.body.data[0].name` | Integration responses should be handled safely as dictionaries. | Use `index()` with safe defaults. |

### Contamination review rule

```text
If SAIL code looks like JavaScript, React, HTML, CSS, Java or Python,
it is not ready for Appian Designer.
```

## SAIL syntax contract

AI-generated SAIL must follow these syntax rules.

| Area | Required Appian pattern |
|---|---|
| Components | Use `a!componentName(parameter: value)` style. |
| Named parameters | Use exact Appian parameter names. |
| Rule inputs | Use `ri!inputName`. |
| Local variables | Use `local!variableName`. |
| Function variables | Use documented function variables such as `fv!item` only in supported contexts. |
| Process variables | Use `pv!` only in process model descriptions, not interface code. |
| Constants | Use `cons!APN_CONS_Name`. |
| Rules | Use `rule!APN_RULE_Name()`. |
| Record types | Use Appian record type references, not strings or made-up paths. |
| Comments | Use Appian expression comments where appropriate. |

### Mandatory syntax exclusions

Reject code containing these in SAIL blocks unless they are inside quoted display text:

```text
function()
const
let
var
return
class
new
this.
=>
===
!==
&&
||
<div
<span
<Button
style={{
fontWeight
className
nullish coalescing
```

## Component and parameter guardrails

AI must only use components and parameters documented for Appian 26.4.

### Component rule

```text
A component is allowed only if it exists in the official Appian SAIL component reference for the target version.
```

### Parameter rule

```text
A parameter is allowed only if that exact component supports that exact parameter name.
```

A parameter that exists on one Appian component must not be copied to another component unless the official documentation confirms it is supported there.

Example review concern:

```text
showBorder may be valid for some layout components but must not be assumed valid for every grid or display component.
```

### Component specification template

When AI introduces a component, it should be able to justify it like this:

```text
Component:        a!richTextItem()
Purpose:          Display styled rich text in a supported rich text field.
Parameters used:  text, style, colour or other documented parameters only.
Official source:  Appian 26.4 SAIL Components documentation.
Risk:             Reject if style uses unsupported values such as "bold".
```

## Allowed-value guardrails

AI must not translate Appian allowed values into natural language, CSS language or another framework's wording.

If Appian expects an allowed value, use the exact Appian value.

### Common value substitution risks

| AI may write | Why this is risky | Appian-safe review action |
|---|---|---|
| `bold` | Appian rich text style values may use documented uppercase values such as `STRONG`, not arbitrary natural language. | Verify the exact allowed value in Appian docs and use only documented values. |
| `primary` | Button style values are Appian-specific. | Use only documented Appian button styles. |
| `secondary` | Often copied from web UI frameworks. | Replace with a documented Appian value only if applicable. |
| `danger` or `destructive` | Common in design systems, not automatically Appian-supported. | Use documented Appian style and validation patterns. |
| `horizontal` | Some components use specific layout values. | Use only documented values for that component. |
| `auto`, `fit-content`, `100%`, `px` widths | CSS sizing is not Appian parameter syntax. | Use documented Appian width values for that component. |
| `className` | React pattern. | Not valid SAIL. Remove. |
| `fontWeight` | CSS/React pattern. | Use documented rich text style values. |
| `onClick` | JavaScript/React event pattern. | Use Appian `saveInto`, `submit`, dynamic links or record actions as documented. |
| `disabledWhen` | Sounds plausible but may not exist on the component. | Verify exact Appian parameter name. |
| `visibleWhen` | Sounds plausible but Appian commonly uses different patterns. | Verify exact Appian parameter and use conditional logic correctly. |

### Strong example: rich text styling

Incorrect AI-style output:

```appian
a!richTextItem(
  text: "Important",
  style: "bold"
)
```

Correct Appian-style output, subject to official value verification:

```appian
a!richTextItem(
  text: "Important",
  style: "STRONG"
)
```

Review rule:

```text
Do not allow natural-language style values such as "bold" unless the official Appian component documentation lists that exact value.
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

### Function allowability test

Before accepting any function, ask:

```text
1. Does this exact function exist in Appian 26.4?
2. Is the function being used in a supported context?
3. Are all named parameters valid for this function?
4. Are the input types correct?
5. Is the return type handled correctly?
6. Is null or empty input handled safely?
```

### Reject invented function names

Reject functions that sound plausible but are not confirmed in the official Appian function reference.

Examples of high-risk AI patterns:

```text
a!stackedLayout()
a!showWhen()
a!formLayoutTitleBar()
a!recordActionTriggerLink()
a!onClick()
a!style()
a!cssClass()
```

Do not use these unless official Appian documentation confirms the function exists in the target version.

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

### Record reference safety

Reject these patterns in SAIL:

```text
"recordType!APN_REC_Claim.fields.claimId"
recordType.APnClaim.fields.claimId
APN_REC_Claim.claimId
record.fields.claimId
```

Use Appian record references inserted by Designer autocomplete or clearly marked placeholders for the developer to resolve.

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
Generate Appian Expression Language and SAIL only.
Do not use Java, JavaScript, React, HTML, CSS or pseudo-code inside SAIL.
Do not invent record fields, groups, Appian functions, SAIL components,
component parameters or allowed values. Include DDL, record types,
expression rules, interfaces, process models, actions, security, tests,
deployment notes and rollback considerations. Mark anything that requires
official Appian documentation verification.
```

### Review SAIL code

```text
Review this SAIL code for Appian 26.4 compatibility. Check for invented
components, unsupported parameters, wrong allowed values, JavaScript/React/
CSS/HTML contamination, unsafe saveInto patterns, null handling,
query-in-loop patterns, refresh behaviour, accessibility and mobile usability.
Return a table with severity, issue, reason and corrected Appian pattern.
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
unsupported Appian syntax, non-Appian language contamination, missing build
detail, unclear assumptions, security gaps, testing gaps, deployment gaps
and places where official Appian documentation verification is required.
```

## Mandatory AI self-check before returning code

Before returning Appian code, AI must answer these checks internally and fix any failures.

```text
1. Does every function exist in Appian 26.4?
2. Does every component exist in Appian 26.4?
3. Does every named parameter belong to that exact component or function?
4. Does every allowed value match Appian wording and casing?
5. Is there any Java, JavaScript, React, HTML, CSS, Python or pseudo-code inside SAIL?
6. Are all query inputs null-safe?
7. Are all date formatting calls null-safe?
8. Are record references Appian-style, not strings or object paths from another language?
9. Does saveInto use Appian patterns rather than onClick or event handlers?
10. Is the code ready to paste into Appian Designer without unsupported syntax?
```

If any answer is uncertain, the code must be labelled as requiring verification, not build-ready.

## Review checklist

| Review area | Pass or fail |
|---|---|
| Uses current project prefix. |  |
| Does not invent Appian objects, functions or parameters. |  |
| Does not use non-Appian syntax inside SAIL. |  |
| Allowed values use Appian wording and casing. |  |
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
| Using `style: "bold"` | Use only documented rich text style values, such as `STRONG` where supported. |
| Using `style: "primary"` | Use only documented Appian button styles for the target component. |
| Using CSS properties such as `fontWeight` | Remove CSS syntax and use Appian component parameters. |
| Using React/HTML tags | Replace with SAIL components. |
| Using `onClick` | Use `saveInto`, `submit`, dynamic links or record actions as supported. |
| Using `&&`, `||` or `!` | Use Appian logical functions and supported operators. |
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

## Rejected substitution table

This table captures high-risk substitutions that AI often makes when it imports habits from other UI frameworks.

| Reject | Why | Safer Appian review position |
|---|---|---|
| `bold` | Natural language/CSS-style value, not automatically a valid Appian value. | Use documented Appian rich text style value only. |
| `italic` | May not match exact Appian allowed value or casing. | Verify exact Appian value before use. |
| `primary` | Common web framework value. | Use documented Appian button style value only. |
| `secondary` | Common web framework value. | Use documented Appian button style value only. |
| `danger` | Common design-system value. | Use documented Appian style, colour or validation pattern only. |
| `destructive` | Common design-system value. | Verify Appian support before use. |
| `horizontal` | May be wrong for Appian component parameters. | Use exact documented layout value. |
| `vertical` | May be wrong for Appian component parameters. | Use exact documented layout value. |
| `AUTO` everywhere | Width values differ by component. | Verify allowed width values per component. |
| `MINIMIZE` everywhere | May be valid for some components and invalid for others. | Verify per component. |
| `className` | React only. | Not valid SAIL. |
| `fontWeight` | CSS only. | Not valid SAIL. |
| `display: flex` | CSS only. | Use Appian layout components. |
| `onClick` | JavaScript/React event handler. | Use Appian `saveInto`, dynamic links or actions. |
| `return` | JavaScript/Java syntax. | Appian expression returns the evaluated expression. |
| `const`, `let`, `var` | JavaScript declarations. | Use `local!` inside `a!localVariables()`. |
| `.map()` | JavaScript array method. | Use `a!forEach()`. |
| `.filter()` | JavaScript array method. | Use Appian query filters or Appian list functions. |
| `.length` | JavaScript property. | Use Appian-supported length/count function. |
| `null` as a JavaScript assumption | Appian null handling must be explicit. | Use Appian null checks and defaults. |

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
