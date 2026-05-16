# LLM Usage Guide for Appian Documentation and Code Review

## Table of contents

1. [Purpose](#purpose)
2. [AI usage note](#ai-usage-note)
3. [What this Git library is for](#what-this-git-library-is-for)
4. [What this Git library is not for](#what-this-git-library-is-not-for)
5. [Recommended file loading order](#recommended-file-loading-order)
6. [Minimum project context required](#minimum-project-context-required)
7. [Application prefix resolution rule](#application-prefix-resolution-rule)
8. [How an LLM should generate Appian documentation](#how-an-llm-should-generate-appian-documentation)
9. [How an LLM should review Appian code](#how-an-llm-should-review-appian-code)
10. [Expected documentation outputs](#expected-documentation-outputs)
11. [Mandatory Appian-native generation rules](#mandatory-appian-native-generation-rules)
12. [Mandatory Appian-native code review rules](#mandatory-appian-native-code-review-rules)
13. [Standard prompt for generating Appian application documentation](#standard-prompt-for-generating-appian-application-documentation)
14. [Standard prompt for generating a feature technical specification](#standard-prompt-for-generating-a-feature-technical-specification)
15. [Standard prompt for reviewing AI-generated Appian output](#standard-prompt-for-reviewing-ai-generated-appian-output)
16. [Standard prompt for Appian code review](#standard-prompt-for-appian-code-review)
17. [Code review output format](#code-review-output-format)
18. [SAIL code review checklist](#sail-code-review-checklist)
19. [Expression rule code review checklist](#expression-rule-code-review-checklist)
20. [Record query code review checklist](#record-query-code-review-checklist)
21. [Process model design review checklist](#process-model-design-review-checklist)
22. [Database script review checklist](#database-script-review-checklist)
23. [Integration code review checklist](#integration-code-review-checklist)
24. [Security review checklist](#security-review-checklist)
25. [Testing review checklist](#testing-review-checklist)
26. [Application documentation structure](#application-documentation-structure)
27. [Feature specification structure](#feature-specification-structure)
28. [LLM decision rules](#llm-decision-rules)
29. [Quality gates before accepting LLM output](#quality-gates-before-accepting-llm-output)
30. [Quality gates before accepting Appian code](#quality-gates-before-accepting-appian-code)
31. [Common failure modes](#common-failure-modes)
32. [Final review checklist](#final-review-checklist)
33. [References](#references)

## Purpose

This document explains how an LLM should use this Git repository as a reference library to generate Appian application documentation, technical specifications, architecture notes, process designs, SAIL interface patterns, database designs, testing plans, delivery artefacts and code review findings.

The goal is to make the LLM produce and review Appian-native, build-ready content without inventing Appian functions, SAIL parameters, process model components, database fields, groups or unsupported UI patterns.

## AI usage note

This file is designed to be loaded into an LLM together with the other Appian design-engine files.

Do not assume the LLM can read external URLs. The important rules, standards and examples must come from the local Markdown files in this repository. Official Appian links are provided for human verification and source traceability only.

`APN` is an example prefix used in this reference library. The LLM must use the application short name supplied in the project prompt or project kit, such as `UMS` for User Management Solution.

## What this Git library is for

Use this repository as an Appian engineering reference library for:

- generating Appian application documentation
- generating build-ready feature technical specifications
- reviewing AI-generated Appian code and designs
- reviewing human-written Appian code before build or merge
- creating database and record type designs
- creating process model designs using Appian-native process components
- creating SAIL interface designs and reusable UI patterns
- creating integration patterns and Web API designs
- creating testing plans and end-to-end test scripts
- creating release and deployment documentation
- enforcing the supplied application prefix, naming, security, performance and governance standards

## What this Git library is not for

This repository is not:

- a replacement for official Appian documentation
- a project-specific source of truth unless a project kit is also provided
- a licence to invent Appian functions or parameters
- a place to copy domain-specific examples into unrelated projects
- a Java, JavaScript, React, HTML or CSS design library
- a generic BPMN modelling guide detached from Appian process model concepts
- a mandate to use `APN` for every project

## Recommended file loading order

When using an LLM, load files in this order.

### Core context

```text
1. docs/appian-26-4/design-engine/README.md
2. docs/appian-26-4/design-engine/00_Core_Methodology.md
3. docs/appian-26-4/design-engine/LLM_USAGE_GUIDE_FOR_APPIAN_DOCUMENTATION.md
4. docs/appian-26-4/design-engine/01_Naming_and_Object_Standards.md
5. docs/appian-26-4/design-engine/AI_REVIEW_GUARDRAILS_APPIAN.md
```

### Design and build standards

```text
6. docs/appian-26-4/design-engine/02_Database_Record_and_Data_Model_Standards.md
7. docs/appian-26-4/design-engine/03_SAIL_Interface_Standards.md
8. docs/appian-26-4/design-engine/04_Process_Model_and_Action_Standards.md
9. docs/appian-26-4/design-engine/APPIAN_PROCESS_MODEL_DESIGN_BEST_PRACTICES.md
10. docs/appian-26-4/design-engine/05_Security_Integration_and_Deployment_Standards.md
11. docs/appian-26-4/design-engine/APPIAN_PATTERN_LIBRARY.md
12. docs/appian-26-4/design-engine/06_AI_Spec_Review_Checklists.md
```

### Function and component reference guidance

```text
13. docs/appian-26-4/Appian_Functions/appian-function-index.md
14. docs/appian-26-4/Appian_Functions/functions.md
15. docs/appian-26-4/Appian_Functions/sail-components.md
16. docs/appian-26-4/Appian_Functions/sail-recipes-ux-best-practices.md
```

### Project-specific context

Load these after the common design engine:

```text
17. Project kit
18. Application name and short name / prefix
19. User stories
20. Existing data model
21. Existing Appian object inventory
22. Existing SAIL or expression rules where modification or review is required
23. Integration request and response payloads
24. Security groups and role model
25. Non-functional requirements
26. Pull request diff or changed files for code review
```

## Minimum project context required

Before an LLM generates Appian documentation or reviews Appian code, the following project context should be provided.

| Context item | Required | Example |
|---|---:|---|
| Application name | Yes | User Management Solution |
| Application short name / prefix | Yes | `UMS` |
| Database prefix | Recommended | `ums` |
| Business domain | Yes | Claims, payments, licensing, service requests |
| User stories | Yes | Story ID, role, action, outcome, acceptance criteria |
| Target Appian version | Yes | Appian 26.4 |
| Existing record types | Yes, if modifying or reviewing an existing app | `UMS_REC_User` |
| Existing tables | Yes, if modifying or reviewing an existing app | `ums_user` |
| Existing groups | Yes | `UMS_GRP_Admins`, `UMS_GRP_UserManagers` |
| Integration contracts | Required if integrations exist | Request and response sample |
| Changed files or code diff | Required for code review | SAIL, expression rules, DDL, process spec, integration spec |
| Deployment environments | Recommended | DEV, TEST, UAT, PROD |
| Out-of-scope items | Recommended | No payment gateway in phase 1 |
| UI constraints | Recommended | Mobile-friendly, portal-safe, accessibility AA |

If the application short name is missing, the LLM should ask for it or use `<PREFIX>` placeholders. It must not silently default to `APN` unless the prompt explicitly says `Application Short Name / Prefix: APN`.

## Application prefix resolution rule

The supplied application short name controls object naming.

Resolution order:

```text
1. Use the explicit Application Short Name / Prefix from the prompt.
2. If the project kit provides a different prefix, use the project kit value.
3. If reviewing an existing app, infer the existing prefix only from the existing object set and flag inconsistencies.
4. If no prefix is provided, ask for it before producing build-ready names.
5. Use <PREFIX> placeholders for draft examples when no prefix is available.
6. Do not blindly use APN. APN is only a reference-library example.
```

Standard input block:

```text
Application Name: User Management Solution
Application Short Name / Prefix: UMS
Database Prefix: ums
Target Appian Version: 26.4
```

LLM validation examples:

| Supplied prefix | Correct object example | Incorrect object example |
|---|---|---|
| `UMS` | `UMS_UI_UserSummary` | `APN_UI_UserSummary` |
| `LSC` | `LSC_QRY_GetClaimById` | `APN_QRY_GetClaimById` |
| `PAY` | `PAY_PM_ApprovePayment` | `APN_PM_ApprovePayment` |

## How an LLM should generate Appian documentation

An LLM should work in this sequence.

```text
1. Read the project goal and user stories.
2. Identify the application name, application short name and database prefix.
3. Identify the business entities and lifecycle.
4. Confirm the naming standard using the supplied prefix.
5. Draft the object inventory.
6. Design the database model first.
7. Map tables to Appian record types and relationships.
8. Define constants and reference data.
9. Define expression rules and query rules.
10. Define process models using Appian process model components.
11. Define SAIL interfaces using Appian-supported SAIL components only.
12. Define integrations and Web APIs where required.
13. Define security model.
14. Define deployment approach.
15. Define unit tests and end-to-end test scripts.
16. Run AI guardrail review before returning output.
```

## How an LLM should review Appian code

For code review, the LLM must behave like an Appian reviewer, not a generic software reviewer.

The review sequence is:

```text
1. Identify the artefact type: SAIL, expression rule, process model design, DDL, integration, Web API, deployment file or documentation.
2. Confirm the expected Appian version and supplied project prefix.
3. Check whether object names use the supplied prefix, not APN unless APN is the project prefix.
4. Check whether the code uses only Appian-native syntax for Appian artefacts.
5. Check functions, components, parameters and allowed values against the local library and official Appian documentation where needed.
6. Check data model, record type and query safety.
7. Check saveInto, refresh and null handling.
8. Check process design, PVs, gateways, Write Records and exception paths where relevant.
9. Check security beyond UI hiding.
10. Check performance, mobile usability, accessibility and testing impact.
11. Return findings as a prioritised review table with corrected Appian patterns.
```

The LLM must not rewrite the entire solution unless asked. A code review should clearly separate critical defects, build-breaking Appian syntax issues, naming-prefix issues, security issues, performance issues, maintainability issues, recommendations and items requiring official Appian verification.

## Expected documentation outputs

For a full Appian application, the LLM should generate these documents where applicable.

| Document | Purpose |
|---|---|
| Application Overview | Business purpose, scope, users and modules. |
| Architecture Document | Appian architecture, data model, integrations, security and environments. |
| Data Model Specification | Tables, relationships, record type mapping and migration scripts. |
| Object Inventory | Appian objects, ownership and build order. |
| Feature Technical Specifications | One build-ready spec per feature or coherent work package. |
| SAIL Interface Specification | Interface purpose, inputs, UX pattern, code and tests. |
| Process Model Specification | Appian-native process design, PVs, nodes, gateways and tests. |
| Integration Specification | Connected systems, request/response, errors and retries. |
| Security Design | Groups, object security, record security and action visibility. |
| Testing Strategy | Unit, integration, regression, accessibility, performance and UAT tests. |
| Deployment Guide | Packages, import customisation, database migration and rollback. |
| Support Guide | Monitoring, incident handling, error queues and operational ownership. |

## Mandatory Appian-native generation rules

The LLM must follow these rules for all generated Appian output.

```text
1. Generate Appian Expression Language and SAIL only for Appian code.
2. Do not use Java, JavaScript, React, HTML, CSS or Python inside SAIL.
3. Do not invent Appian functions.
4. Do not invent SAIL components.
5. Do not invent component parameters.
6. Do not invent allowed values such as bold, primary or onClick.
7. Do not invent record fields, tables, groups or process variables.
8. Use the application short name / prefix supplied in the prompt or project kit.
9. Do not default to APN unless APN is explicitly supplied as the project prefix.
10. Use Appian process model components for workflow design.
11. Include tests and review checklists.
```

## Mandatory Appian-native code review rules

When reviewing code, the LLM must check for Appian-native correctness first.

```text
1. Reject non-Appian syntax inside SAIL or expression rules.
2. Reject invented Appian functions, components, parameters and allowed values.
3. Reject JavaScript-style event handling such as onClick inside SAIL.
4. Reject CSS-style parameters such as fontWeight, className or display:flex inside SAIL.
5. Reject React or HTML tags inside SAIL.
6. Reject process designs that describe Java services instead of Appian process nodes.
7. Reject record queries that read fields not listed in the fields parameter.
8. Reject unsafe null handling that can break when inputs are blank.
9. Reject query-in-loop patterns unless explicitly justified and reviewed.
10. Reject security patterns that rely only on hiding a button or section.
11. Reject object names that use APN when a different project prefix was supplied.
```

## Standard prompt for generating Appian application documentation

Use this prompt when asking an LLM to generate complete Appian application documentation.

```text
You are generating Appian application documentation using this Appian
engineering library from the Git repository.

Project input:
Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Database Prefix: <database_prefix>
Target Appian Version: 26.4

Read and apply the local Markdown files in this order:
1. LLM_USAGE_GUIDE_FOR_APPIAN_DOCUMENTATION.md
2. 00_Core_Methodology.md
3. 01_Naming_and_Object_Standards.md
4. AI_REVIEW_GUARDRAILS_APPIAN.md
5. 02_Database_Record_and_Data_Model_Standards.md
6. 03_SAIL_Interface_Standards.md
7. APPIAN_PROCESS_MODEL_DESIGN_BEST_PRACTICES.md
8. APPIAN_PATTERN_LIBRARY.md
9. Appian_Functions/functions.md
10. Appian_Functions/sail-components.md
11. Appian_Functions/sail-recipes-ux-best-practices.md

Use the supplied prefix for all Appian objects and database names.
Do not use APN unless the supplied prefix is APN.
Use the project kit, user stories and existing Appian context provided below.
Do not invent Appian functions, SAIL components, SAIL parameters, process
model capabilities, record fields, groups, database columns or integrations.
Generate Appian-native documentation only.

Produce the following outputs:
1. Application overview
2. Architecture design
3. Data model and record type design
4. Appian object inventory
5. Process model design using Appian process components
6. SAIL interface design using Appian-supported components
7. Integration design where applicable
8. Security model
9. Testing strategy
10. Deployment and rollback guide
11. Build checklist
12. End-to-end test script

Mark any item that requires official Appian documentation verification.
```

## Standard prompt for generating a feature technical specification

Use this prompt for one feature or story group.

```text
Create a build-ready Appian technical specification for the feature below.
Use this Appian engineering library as the design standard.

Project input:
Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Database Prefix: <database_prefix>
Target Appian Version: 26.4

Rules:
- Use the supplied prefix for all Appian object names.
- Do not use APN unless the supplied prefix is APN.
- Use Appian Expression Language, SAIL and Appian process model concepts only.
- Do not use Java, JavaScript, React, HTML, CSS or pseudo-code inside SAIL.
- Do not invent Appian functions, SAIL components, parameters or allowed values.
- Do not invent tables, fields, groups or record types not supplied in the project context.
- Where a design decision has multiple valid options, show options and recommend one.
- Mark assumptions clearly.

The specification must include:
1. Header and purpose
2. Story references and scope
3. Design decisions
4. Dependencies
5. Object inventory
6. DDL and migration scripts
7. Record type mapping and relationships
8. Constants and reference data
9. Expression rules and query rules
10. SAIL interfaces with code patterns
11. Process model design with Appian nodes, PVs, gateways and Write Records
12. Record actions and related actions
13. Security model
14. Integration or Web API design where applicable
15. Unit tests by layer
16. End-to-end implementation checklist
17. End-to-end test script
18. Deployment and rollback notes
```

## Standard prompt for reviewing AI-generated Appian output

Use this prompt to check another AI output before build.

```text
Review this Appian output against this Appian engineering library.
Use the supplied Application Short Name / Prefix as the naming standard.
Do not treat APN as valid unless APN is the supplied prefix.
Return a severity table with issue, location, reason and corrected Appian pattern.

Check for:
1. Incorrect application prefix usage.
2. Invented Appian functions.
3. Invented SAIL components or parameters.
4. Non-Appian syntax, including Java, JavaScript, React, HTML, CSS or Python.
5. Wrong allowed values, such as bold instead of Appian-supported rich text values.
6. Unsafe saveInto patterns.
7. Missing null safety.
8. Query-in-loop patterns.
9. Missing fields in a!queryRecordType calls.
10. Non-Appian process design terminology.
11. Missing process variables, gateways, Write Records or exception paths.
12. Missing security beyond UI hiding.
13. Missing tests, deployment notes or rollback guidance.
```

## Standard prompt for Appian code review

Use this prompt for reviewing actual Appian code, pull requests or changed files.

```text
Perform an Appian 26.4 code review using this Appian engineering library.
Review only the supplied code and project context. Do not invent missing
objects or assume undocumented fields exist.

Project input:
Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Database Prefix: <database_prefix>
Target Appian Version: 26.4

Classify each finding as Critical, High, Medium, Low or Recommendation.
For every issue, provide:
- artefact or file name
- location or code snippet
- issue description
- why it matters in Appian
- corrected Appian pattern using the supplied prefix
- whether official Appian documentation verification is required

Review these areas:
1. Naming prefix consistency.
2. SAIL syntax and component validity.
3. Appian function validity.
4. Named parameter validity.
5. Allowed value correctness, including values such as STRONG and SOLID.
6. Non-Appian syntax contamination from Java, JavaScript, React, HTML, CSS or Python.
7. saveInto, local variable and refresh behaviour.
8. Query safety, including fields, paging, filters and null guards.
9. Process model design, including PVs, RIs, gateways, Write Records and exception paths.
10. Database design, DDL safety, audit fields, keys and migration risk.
11. Integration request and response handling.
12. Security controls beyond UI visibility.
13. Performance, accessibility, mobile compatibility and testing impact.
```

## Code review output format

Return code review findings in this table format.

| Severity | Artefact | Location | Issue | Why it matters | Correct Appian pattern | Verification needed |
|---|---|---|---|---|---|---|
| Critical | `<PREFIX>_UI_CreateClaim` | Submit button | Uses unsupported `onClick` pattern | Appian SAIL does not use JavaScript event handlers | Use `saveInto` and `submit` on `a!buttonWidget()` | Check component docs |
| High | `APN_QRY_GetUserById` | Object name | Uses `APN` but project prefix is `UMS` | Naming mismatch creates inconsistent application object ownership | Rename to `UMS_QRY_GetUserById` | No |

Severity definitions:

| Severity | Meaning |
|---|---|
| Critical | Will break Appian evaluation, cause data corruption or create a serious security issue. |
| High | Likely runtime failure, incorrect data write, broken security, prefix inconsistency or major performance issue. |
| Medium | Maintainability, incomplete validation, weak UX, missing tests or avoidable risk. |
| Low | Minor consistency, readability or documentation issue. |
| Recommendation | Improvement that is not required for correctness. |

## SAIL code review checklist

| Check | Pass or fail |
|---|---|
| Object names use the supplied application prefix. |  |
| Uses Appian SAIL components only. |  |
| No React, HTML, CSS, JavaScript, Java or Python syntax inside SAIL. |  |
| Every component exists in Appian 26.4 or is marked for verification. |  |
| Every named parameter belongs to that exact component. |  |
| Every allowed value uses Appian-supported wording and casing. |  |
| Rich text styling does not use unsupported values such as `bold`. |  |
| Buttons use Appian-supported behaviour, not `onClick`. |  |
| Editable forms use local working copies where appropriate. |  |
| Submit buttons write local working values back to rule inputs. |  |
| Cancel buttons avoid validation where appropriate. |  |
| Required fields and business validations are clear. |  |
| Optional values are null-safe. |  |
| Date formatting is null-safe. |  |
| Refresh behaviour is intentional. |  |
| Large queries and slow integrations do not run on every refresh. |  |
| Mobile layout has been considered. |  |
| Accessibility labels, instructions and validation messages are clear. |  |

## Expression rule code review checklist

| Check | Pass or fail |
|---|---|
| Rule name follows supplied prefix and project naming convention. |  |
| Rule inputs are minimal and correctly typed. |  |
| Null inputs are handled safely. |  |
| Return type is clear. |  |
| The rule does not mix Appian syntax with Java or JavaScript syntax. |  |
| Complex logic is readable and testable. |  |
| Repeated code is extracted into reusable rules where useful. |  |
| The rule has unit tests for null, empty, happy path and boundary cases. |  |

## Record query code review checklist

| Check | Pass or fail |
|---|---|
| Uses `a!queryRecordType()` for record-backed queries where suitable. |  |
| `fields` includes every field the caller reads. |  |
| Filters use valid field references. |  |
| Filter values match field types. |  |
| Equality filters are null-guarded. |  |
| `a!sortInfo()` is placed inside paging configuration where applicable. |  |
| `fetchTotalCount` is used only where needed. |  |
| Soft-deleted records are excluded where required. |  |
| Query-in-loop patterns are avoided. |  |
| Paging and batch size are appropriate. |  |

## Process model design review checklist

| Check | Pass or fail |
|---|---|
| Process model name uses the supplied prefix. |  |
| Process uses Appian-native process components only. |  |
| Trigger is clearly defined. |  |
| Process variables are listed with type, parameter flag and purpose. |  |
| Start form or user input task mapping is documented. |  |
| Interfaces use `ri!`; process models use `pv!`. |  |
| Cancel path is explicit where required. |  |
| Gateway conditions are clear and testable. |  |
| Write Records nodes identify record type, source PV and output mapping. |  |
| Child writes occur after parent IDs are available. |  |
| Integration nodes include success and failure paths. |  |
| Alerts and data management settings are documented. |  |
| Process start security aligns with record action visibility. |  |
| Unit tests cover every branch. |  |

## Database script review checklist

| Check | Pass or fail |
|---|---|
| Table names follow supplied database prefix and naming convention. |  |
| Primary keys are meaningful and not generic `id`. |  |
| Foreign keys are documented and named clearly. |  |
| Reference data is separated from free-text workflow values. |  |
| Audit fields are present where required. |  |
| Soft delete strategy is defined where required. |  |
| Version or optimistic locking field is considered where concurrent edits exist. |  |
| Indexes support expected joins, filters and date ranges. |  |
| Seed data uses safe fabricated values. |  |
| Migration and rollback notes are included. |  |

## Integration code review checklist

| Check | Pass or fail |
|---|---|
| Connected system and integration object use the supplied prefix. |  |
| Authentication and credential handling are documented. |  |
| Request payload is documented. |  |
| Response payload is based on real or representative sample. |  |
| Dictionary access uses `index()` with safe defaults. |  |
| Response mapping does not assume typed records unless explicitly mapped. |  |
| Retry and idempotency are considered for state-changing calls. |  |
| Timeout and error paths are documented. |  |
| Sensitive data is not logged unsafely. |  |

## Security review checklist

| Check | Pass or fail |
|---|---|
| Group names use the supplied prefix where project naming requires it. |  |
| Security is not enforced only by hiding UI components. |  |
| Record type security is documented. |  |
| Record action visibility is documented. |  |
| Process start security is documented. |  |
| Folder and object security are considered. |  |
| Web API authentication and authorisation are documented. |  |
| Connected system credentials are not hardcoded. |  |
| Support access and audit visibility are controlled. |  |
| Sensitive fields are masked or restricted where required. |  |

## Testing review checklist

| Check | Pass or fail |
|---|---|
| SAIL interface tests include load, validation, submit and cancel paths. |  |
| Expression rule tests include null, empty, happy path and boundary cases. |  |
| Process model tests cover all gateways. |  |
| Integration tests cover success, error, timeout and malformed response. |  |
| Security tests include authorised and unauthorised users. |  |
| Performance tests cover large data and grid behaviour where relevant. |  |
| Accessibility tests cover labels, validation and keyboard/screen reader considerations. |  |
| End-to-end test script maps to the user story. |  |

## Application documentation structure

A full Appian application document should follow this structure.

```text
1. Executive summary
2. Business scope
3. User roles and personas
4. Appian application architecture
5. Module overview
6. Data model overview
7. Record type architecture
8. Process model architecture
9. Interface and UX architecture
10. Integration architecture
11. Security and governance model
12. Reporting and dashboard approach
13. Error handling and support model
14. Deployment and environment strategy
15. Testing strategy
16. Release checklist
17. Open decisions and assumptions
```

## Feature specification structure

A build-ready feature spec should follow this structure.

```text
1. Specification header
2. User stories and acceptance criteria
3. Design decisions
4. Dependencies on existing objects
5. Object inventory
6. Database DDL
7. Record types
8. Constants and reference data
9. Expression rules
10. Interfaces
11. Process models
12. Record actions and related actions
13. Integration or Web API wiring
14. Security
15. Unit tests
16. End-to-end implementation checklist
17. End-to-end test script
18. Deployment notes
```

## LLM decision rules

Use these rules when the LLM needs to make design choices.

| Situation | Required behaviour |
|---|---|
| Application short name is missing | Ask for it or use `<PREFIX>` placeholders. Do not silently use `APN`. |
| Data model is missing | Ask for it or propose options clearly marked as assumptions. |
| Existing SAIL must be modified | Ask for the current SAIL before generating exact insertion code. |
| Integration response is missing | Ask for a sample response before writing `index()` logic. |
| Security groups are missing | Ask for the role model or create a clearly labelled placeholder. |
| Appian function is uncertain | Mark as requiring verification. |
| Component parameter is uncertain | Do not include it in build-ready code. |
| Multiple architecture options exist | Present options, trade-offs and recommendation. |
| Requirement conflicts with Appian best practice | Call out the conflict and propose a safer Appian pattern. |

## Quality gates before accepting LLM output

An LLM output is not ready for build unless it passes these gates.

| Gate | Requirement |
|---|---|
| Prefix gate | Object names use the supplied application short name / prefix. |
| Appian syntax gate | No non-Appian code appears inside SAIL or expressions. |
| Function gate | Every function exists in Appian 26.4 or is marked for verification. |
| Component gate | Every component and parameter is Appian-supported or marked for verification. |
| Data gate | Tables, fields and relationships are documented. |
| Record gate | Record type mapping and relationships are defined. |
| Process gate | Process model uses Appian nodes, PVs, gateways and Write Records. |
| Security gate | Security is enforced beyond UI visibility. |
| Performance gate | Queries, refresh behaviour and grids are performance-aware. |
| Testing gate | Unit and end-to-end tests are included. |
| Deployment gate | Release, migration and rollback notes are included. |

## Quality gates before accepting Appian code

Appian code is not ready for merge or build until it passes these code review gates.

| Gate | Requirement |
|---|---|
| Prefix gate | New and changed Appian objects use the supplied application prefix. |
| Build syntax gate | Code is Appian-native and does not contain unsupported language contamination. |
| Component gate | SAIL components and parameters are verified or marked for verification. |
| Function gate | Functions are valid and used in supported contexts. |
| Query gate | Record queries include fields, paging, safe filters and no avoidable query-in-loop. |
| Save gate | Forms use safe `saveInto` and working-copy patterns. |
| Process gate | Process designs include PVs, RI mappings, gateways, writes and exception paths. |
| Data gate | DDL and record mappings are consistent. |
| Security gate | Access is enforced at Appian security layers, not only UI conditions. |
| Test gate | Tests cover happy, error, security and edge paths. |
| Deployment gate | Migration and rollback impact is known. |

## Common failure modes

| Failure mode | Why it matters | Correction |
|---|---|---|
| LLM uses `APN` when project prefix is `UMS` | Object ownership and naming validation become inconsistent. | Use the supplied prefix from the prompt or project kit. |
| LLM writes React-style UI | Appian Designer will reject it. | Use SAIL components only. |
| LLM writes `bold` for rich text style | Appian allowed values are specific. | Verify and use Appian-supported value such as `STRONG` where applicable. |
| LLM invents a component parameter | Appian evaluation fails. | Verify exact parameter from official docs. |
| LLM omits query fields | Query result may only include primary key. | Specify every field read by the caller. |
| LLM creates Java service layer | Not Appian-native process design. | Use process model nodes and expression rules. |
| LLM hides action only in UI | Security bypass risk. | Configure record action visibility and process start security. |
| LLM assumes integration response shape | Runtime indexing failures. | Request response sample and use safe `index()` handling. |
| LLM omits deployment notes | Build may not promote safely. | Add package, database migration and rollback notes. |
| LLM gives generic code review only | It misses Appian-specific runtime failures. | Apply the Appian code review checklists in this guide. |

## Final review checklist

| Review item | Pass or fail |
|---|---|
| Local Git library files were used as context. |  |
| Project kit was provided. |  |
| Application name is provided. |  |
| Application short name / prefix is provided and used. |  |
| Database prefix is provided or derived correctly. |  |
| No unsupported Appian syntax is present. |  |
| No non-Appian code is mixed into SAIL. |  |
| No invented components, parameters or functions are present. |  |
| Data model is clear. |  |
| Record types and relationships are clear. |  |
| Process models use Appian-native components. |  |
| Interfaces use SAIL components only. |  |
| Integrations include request and response contracts. |  |
| Security model is documented. |  |
| Testing strategy is documented. |  |
| Deployment and rollback are documented. |  |
| Code review checklist has been applied where code is supplied. |  |
| Assumptions are clearly marked. |  |

## References

Local repository references:

- `docs/appian-26-4/design-engine/00_Core_Methodology.md`
- `docs/appian-26-4/design-engine/01_Naming_and_Object_Standards.md`
- `docs/appian-26-4/design-engine/AI_REVIEW_GUARDRAILS_APPIAN.md`
- `docs/appian-26-4/design-engine/APPIAN_PROCESS_MODEL_DESIGN_BEST_PRACTICES.md`
- `docs/appian-26-4/design-engine/APPIAN_PATTERN_LIBRARY.md`
- `docs/appian-26-4/Appian_Functions/functions.md`
- `docs/appian-26-4/Appian_Functions/sail-components.md`
- `docs/appian-26-4/Appian_Functions/sail-recipes-ux-best-practices.md`

Official Appian documentation remains the source of truth:

- https://docs.appian.com/suite/help/26.4/
- https://docs.appian.com/suite/help/26.4/Appian_Functions.html
- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/SAIL_Recipes.html
- https://docs.appian.com/suite/help/26.4/Records.html
- https://docs.appian.com/suite/help/26.4/Process_Modeling.html
