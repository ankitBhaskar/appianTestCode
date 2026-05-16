# LLM Usage Guide for Appian Documentation Generation

## Table of contents

1. [Purpose](#purpose)
2. [AI usage note](#ai-usage-note)
3. [What this Git library is for](#what-this-git-library-is-for)
4. [What this Git library is not for](#what-this-git-library-is-not-for)
5. [Recommended file loading order](#recommended-file-loading-order)
6. [Minimum project context required](#minimum-project-context-required)
7. [How an LLM should generate Appian documentation](#how-an-llm-should-generate-appian-documentation)
8. [Expected documentation outputs](#expected-documentation-outputs)
9. [Mandatory Appian-native generation rules](#mandatory-appian-native-generation-rules)
10. [Standard prompt for generating Appian application documentation](#standard-prompt-for-generating-appian-application-documentation)
11. [Standard prompt for generating a feature technical specification](#standard-prompt-for-generating-a-feature-technical-specification)
12. [Standard prompt for reviewing AI-generated Appian output](#standard-prompt-for-reviewing-ai-generated-appian-output)
13. [Application documentation structure](#application-documentation-structure)
14. [Feature specification structure](#feature-specification-structure)
15. [LLM decision rules](#llm-decision-rules)
16. [Quality gates before accepting LLM output](#quality-gates-before-accepting-llm-output)
17. [Common failure modes](#common-failure-modes)
18. [Final review checklist](#final-review-checklist)
19. [References](#references)

## Purpose

This document explains how an LLM should use this Git repository as a reference library to generate Appian application documentation, technical specifications, architecture notes, process designs, SAIL interface patterns, database designs, testing plans and delivery artefacts.

The goal is to make the LLM produce Appian-native, build-ready documentation without inventing Appian functions, SAIL parameters, process model components, database fields, groups or unsupported UI patterns.

## AI usage note

This file is designed to be loaded into an LLM together with the other APN Appian design-engine files.

Do not assume the LLM can read external URLs. The important rules, standards and examples must come from the local Markdown files in this repository. Official Appian links are provided for human verification and source traceability only.

## What this Git library is for

Use this repository as an Appian engineering reference library for:

- generating Appian application documentation
- generating build-ready feature technical specifications
- reviewing AI-generated Appian code and designs
- creating database and record type designs
- creating process model designs using Appian-native process components
- creating SAIL interface designs and reusable UI patterns
- creating integration patterns and Web API designs
- creating testing plans and end-to-end test scripts
- creating release and deployment documentation
- enforcing APN naming, security, performance and governance standards

## What this Git library is not for

This repository is not:

- a replacement for official Appian documentation
- a project-specific source of truth unless a project kit is also provided
- a licence to invent Appian functions or parameters
- a place to copy domain-specific examples into unrelated projects
- a Java, JavaScript, React, HTML or CSS design library
- a generic BPMN modelling guide detached from Appian process model concepts

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
18. User stories
19. Existing data model
20. Existing Appian object inventory
21. Existing SAIL or expression rules where modification is required
22. Integration request and response payloads
23. Security groups and role model
24. Non-functional requirements
```

## Minimum project context required

Before an LLM generates Appian documentation, the following project context should be provided.

| Context item | Required | Example |
|---|---:|---|
| Application prefix | Yes | `APN` |
| Business domain | Yes | Claims, payments, licensing, service requests |
| User stories | Yes | Story ID, role, action, outcome, acceptance criteria |
| Target Appian version | Yes | Appian 26.4 |
| Existing record types | Yes, if modifying an existing app | `APN_REC_Claim` |
| Existing tables | Yes, if modifying an existing app | `apn_claim` |
| Existing groups | Yes | `APN_GRP_Admins`, `APN_GRP_CaseManagers` |
| Integration contracts | Required if integrations exist | Request and response sample |
| Deployment environments | Recommended | DEV, TEST, UAT, PROD |
| Out-of-scope items | Recommended | No payment gateway in phase 1 |
| UI constraints | Recommended | Mobile-friendly, portal-safe, accessibility AA |

If required context is missing, the LLM should ask targeted questions or mark assumptions clearly. It must not silently invent missing fields, groups, record types, tables or integrations.

## How an LLM should generate Appian documentation

An LLM should work in this sequence.

```text
1. Read the project goal and user stories.
2. Identify the business entities and lifecycle.
3. Confirm the application prefix and naming standard.
4. Draft the object inventory.
5. Design the database model first.
6. Map tables to Appian record types and relationships.
7. Define constants and reference data.
8. Define expression rules and query rules.
9. Define process models using Appian process model components.
10. Define SAIL interfaces using Appian-supported SAIL components only.
11. Define integrations and Web APIs where required.
12. Define security model.
13. Define deployment approach.
14. Define unit tests and end-to-end test scripts.
15. Run AI guardrail review before returning output.
```

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

The LLM must follow these rules for all Appian output.

```text
1. Generate Appian Expression Language and SAIL only for Appian code.
2. Do not use Java, JavaScript, React, HTML, CSS or Python inside SAIL.
3. Do not invent Appian functions.
4. Do not invent SAIL components.
5. Do not invent component parameters.
6. Do not invent allowed values such as bold, primary or onClick.
7. Do not invent record fields, tables, groups or process variables.
8. Use APN naming standards unless the project kit provides another prefix.
9. Use Appian process model components for workflow design.
10. Include tests and review checklists.
```

## Standard prompt for generating Appian application documentation

Use this prompt when asking an LLM to generate complete Appian application documentation.

```text
You are generating Appian application documentation using the APN Appian
engineering library from this Git repository.

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
Use the APN Appian engineering library as the design standard.

Rules:
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
Review this Appian output against the APN Appian engineering library.
Return a severity table with issue, location, reason and corrected Appian pattern.

Check for:
1. Invented Appian functions.
2. Invented SAIL components or parameters.
3. Non-Appian syntax, including Java, JavaScript, React, HTML, CSS or Python.
4. Wrong allowed values, such as bold instead of Appian-supported rich text values.
5. Unsafe saveInto patterns.
6. Missing null safety.
7. Query-in-loop patterns.
8. Missing fields in a!queryRecordType calls.
9. Non-Appian process design terminology.
10. Missing process variables, gateways, Write Records or exception paths.
11. Missing security beyond UI hiding.
12. Missing tests, deployment notes or rollback guidance.
```

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

## Common failure modes

| Failure mode | Why it matters | Correction |
|---|---|---|
| LLM writes React-style UI | Appian Designer will reject it. | Use SAIL components only. |
| LLM writes `bold` for rich text style | Appian allowed values are specific. | Verify and use Appian-supported value such as `STRONG` where applicable. |
| LLM invents a component parameter | Appian evaluation fails. | Verify exact parameter from official docs. |
| LLM omits query fields | Query result may only include primary key. | Specify every field read by the caller. |
| LLM creates Java service layer | Not Appian-native process design. | Use process model nodes and expression rules. |
| LLM hides action only in UI | Security bypass risk. | Configure record action visibility and process start security. |
| LLM assumes integration response shape | Runtime indexing failures. | Request response sample and use safe `index()` handling. |
| LLM omits deployment notes | Build may not promote safely. | Add package, database migration and rollback notes. |

## Final review checklist

| Review item | Pass or fail |
|---|---|
| Local Git library files were used as context. |  |
| Project kit was provided. |  |
| Application prefix is correct. |  |
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
