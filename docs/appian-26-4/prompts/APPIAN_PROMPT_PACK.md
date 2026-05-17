# Appian Prompt Pack

## Table of contents

1. [Purpose](#purpose)
2. [How to use this prompt pack](#how-to-use-this-prompt-pack)
3. [Global Appian LLM instruction](#global-appian-llm-instruction)
4. [Prompt 1: Create full application build blueprint](#prompt-1-create-full-application-build-blueprint)
5. [Prompt 2: Create architecture document](#prompt-2-create-architecture-document)
6. [Prompt 3: Create feature technical specification](#prompt-3-create-feature-technical-specification)
7. [Prompt 4: Create database and record type design](#prompt-4-create-database-and-record-type-design)
8. [Prompt 5: Create SAIL interface design](#prompt-5-create-sail-interface-design)
9. [Prompt 6: Create process model design](#prompt-6-create-process-model-design)
10. [Prompt 7: Create integration design](#prompt-7-create-integration-design)
11. [Prompt 8: Create security matrix](#prompt-8-create-security-matrix)
12. [Prompt 9: Create build execution checklist](#prompt-9-create-build-execution-checklist)
13. [Prompt 10: Create end-to-end test script](#prompt-10-create-end-to-end-test-script)
14. [Prompt 11: Review Appian SAIL code](#prompt-11-review-appian-sail-code)
15. [Prompt 12: Review Appian process model design](#prompt-12-review-appian-process-model-design)
16. [Prompt 13: Review Appian database scripts](#prompt-13-review-appian-database-scripts)
17. [Prompt 14: Review full Appian application design](#prompt-14-review-full-appian-application-design)
18. [Prompt 15: Generate documentation from existing Appian code](#prompt-15-generate-documentation-from-existing-appian-code)

## Purpose

This prompt pack provides copy-paste prompts for using an LLM with this Appian engineering repository.

The prompts are designed to make the LLM generate Appian-native architecture, build plans, technical specifications, code reviews and test artefacts.

## How to use this prompt pack

Before using any prompt, provide the Project Kit or at least the project input block.

Minimum input block:

```text
Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Database Prefix: <database_prefix>
Target Appian Version: 26.4
Users and Roles: <USERS_AND_ROLES>
Scope: <SCOPE>
Integrations: <INTEGRATIONS_OR_NONE>
Mobile Requirements: <MOBILE_REQUIREMENTS>
Reporting Requirements: <REPORTING_REQUIREMENTS>
```

## Global Appian LLM instruction

Add this instruction to every Appian prompt where possible.

```text
Use the local Appian engineering library as context.
Use the supplied Application Short Name / Prefix for all Appian object names.
Use the supplied Database Prefix for all physical table names.
Do not default to APN unless APN is explicitly supplied as the project prefix.
Use Appian Expression Language, SAIL and Appian process model concepts only.
Do not use Java, JavaScript, React, HTML, CSS or Python inside SAIL.
Do not invent Appian functions, SAIL components, component parameters, allowed values, record fields, groups, process variables, database columns or integrations.
If official Appian verification is required, mark the item clearly instead of inventing details.
```

## Prompt 1: Create full application build blueprint

```text
You are an Appian solution architect and technical lead.
Use the local Appian engineering library as context, especially:
- APPIAN_APPLICATION_BUILD_BLUEPRINT.md
- ARCHITECTURE_DOCUMENT_TEMPLATE.md
- PROJECT_KIT_TEMPLATE.md
- 01_Naming_and_Object_Standards.md
- AI_REVIEW_GUARDRAILS_APPIAN.md
- APPIAN_PROCESS_MODEL_DESIGN_BEST_PRACTICES.md
- APPIAN_PATTERN_LIBRARY.md

Create a full Appian development execution plan for the application below.
The plan must start with a detailed architecture document and include Mermaid diagrams.
The plan must be detailed enough for a developer to build in Appian Designer.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Database Prefix: <database_prefix>
Target Appian Version: 26.4
Users and Roles: <USERS_AND_ROLES>
Scope: <SCOPE>
Integrations: <INTEGRATIONS_OR_NONE>
Mobile Requirements: <MOBILE_REQUIREMENTS>
Reporting Requirements: <REPORTING_REQUIREMENTS>

Return the full 21-section build blueprint:
1. Detailed architecture document
2. Application definition
3. Scope and assumptions
4. User roles and security groups
5. Business capability map
6. Data model design
7. Database DDL execution plan
8. Record type design
9. Constants and reference data
10. Expression rules and query rules
11. SAIL interface design
12. Process model design
13. Record actions and related actions
14. Integrations and Web APIs
15. Reporting and dashboards
16. Error handling and audit design
17. Appian application structure
18. Build execution sequence
19. Unit test plan
20. End-to-end test script
21. Deployment and release plan

Apply the global Appian LLM instruction.
```

## Prompt 2: Create architecture document

```text
Create a detailed Appian architecture document using ARCHITECTURE_DOCUMENT_TEMPLATE.md.

Project input:
Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Database Prefix: <database_prefix>
Target Appian Version: 26.4
Users and Roles: <USERS_AND_ROLES>
Scope: <SCOPE>
Integrations: <INTEGRATIONS_OR_NONE>

The document must include:
- architecture overview
- solution context
- business operating model
- user journey
- logical application architecture
- Appian object architecture
- data architecture
- record type architecture
- process architecture
- interface and UX architecture
- integration architecture
- security architecture
- reporting architecture
- error handling and support architecture
- deployment architecture
- architecture decisions
- risks and mitigations

Include Mermaid diagrams using Appian concepts.
Apply the global Appian LLM instruction.
```

## Prompt 3: Create feature technical specification

```text
Create a build-ready Appian feature technical specification using FEATURE_TECH_SPEC_TEMPLATE.md.

Project input:
Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Database Prefix: <database_prefix>
Target Appian Version: 26.4

Feature:
<FEATURE_DESCRIPTION>

User Stories:
<USER_STORIES>

Existing Context:
<EXISTING_TABLES_RECORDS_RULES_INTERFACES_PROCESSES>

The specification must include:
- story references and acceptance criteria
- scope and assumptions
- design decisions
- dependencies
- object inventory
- database DDL
- record types
- constants and reference data
- expression rules and query rules
- SAIL interfaces
- process models
- record actions and related actions
- integrations and Web APIs where applicable
- security
- performance considerations
- unit tests
- end-to-end implementation checklist
- end-to-end test script
- deployment notes

Apply the global Appian LLM instruction.
```

## Prompt 4: Create database and record type design

```text
Create an Appian database and record type design for the feature below.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Database Prefix: <database_prefix>
Target Appian Version: 26.4
Feature: <FEATURE_DESCRIPTION>

Return:
1. Data model overview
2. Table list
3. Full table definitions
4. Primary keys and foreign keys
5. Index strategy
6. Audit fields
7. Soft delete strategy
8. Reference data tables
9. Record type mapping
10. Record relationships
11. Record actions and related actions
12. Security considerations
13. Unit tests
14. Migration and rollback notes

Apply the global Appian LLM instruction.
```

## Prompt 5: Create SAIL interface design

```text
Create an Appian SAIL interface design for the screen below.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Target Appian Version: 26.4
Interface Purpose: <INTERFACE_PURPOSE>
Primary User: <ROLE>
Data Inputs: <RECORD_TYPES_AND_RULE_INPUTS>
Process Context: <PROCESS_MODEL_OR_RECORD_ACTION_CONTEXT>
Mobile Requirement: <MOBILE_REQUIREMENT>

Return:
1. Interface name
2. Purpose
3. Rule inputs
4. Local variables
5. Component structure
6. saveInto behaviour
7. Refresh behaviour
8. Validation rules
9. Empty states
10. Accessibility notes
11. Mobile notes
12. SAIL code skeleton using Appian components only
13. Unit tests

Apply the global Appian LLM instruction.
```

## Prompt 6: Create process model design

```text
Create an Appian process model design using APPIAN_PROCESS_MODEL_DESIGN_BEST_PRACTICES.md.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Target Appian Version: 26.4
Process Purpose: <PROCESS_PURPOSE>
Trigger: <RECORD_ACTION_RELATED_ACTION_TIMER_WEB_API_SUBPROCESS>
Interfaces: <INTERFACES>
Record Types: <RECORD_TYPES>
Integrations: <INTEGRATIONS_OR_NONE>

Return:
1. Process model name
2. Process display name
3. Trigger
4. Process variables with type, parameter flag, default and purpose
5. Rule input mapping between PVs and RIs
6. Step-by-step process flow
7. Gateway logic
8. Write Records nodes
9. Subprocesses
10. Integration nodes and error paths
11. Alerts and data management
12. Security
13. Mermaid process diagram
14. Unit tests and end-to-end tests

Apply the global Appian LLM instruction.
```

## Prompt 7: Create integration design

```text
Create an Appian integration design for the integration below.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Target Appian Version: 26.4
Integration Purpose: <PURPOSE>
Direction: Inbound / Outbound
External System: <SYSTEM>
Method: <GET_POST_PUT_PATCH_DELETE>
Request Sample: <REQUEST_SAMPLE_OR_NOT_AVAILABLE>
Response Sample: <RESPONSE_SAMPLE_OR_NOT_AVAILABLE>
Authentication: <AUTHENTICATION>

Return:
1. Connected system design
2. Integration object design
3. Request contract
4. Response contract
5. Mapping rules
6. Error handling
7. Retry and idempotency rules
8. Security
9. Logging and audit
10. Process model usage
11. Unit and integration tests

If request or response samples are missing, do not invent payload structure. Mark as requiring verification.
Apply the global Appian LLM instruction.
```

## Prompt 8: Create security matrix

```text
Create an Appian security matrix for the application below.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Target Appian Version: 26.4
Roles: <ROLES>
Objects: <OBJECTS_OR_MODULES>
Security Rules: <SECURITY_RULES>

Return:
1. Group architecture
2. Site access matrix
3. Folder security matrix
4. Object security matrix
5. Record type security matrix
6. Record action visibility matrix
7. Process model start security matrix
8. Web API security matrix
9. Connected system security notes
10. Document folder security
11. Support and admin access rules
12. Security test cases

Apply the global Appian LLM instruction.
```

## Prompt 9: Create build execution checklist

```text
Create an Appian build execution checklist using BUILD_EXECUTION_CHECKLIST_TEMPLATE.md.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Database Prefix: <database_prefix>
Target Appian Version: 26.4
Scope: <SCOPE>
Object Inventory: <OBJECT_INVENTORY>

Return a dependency-ordered checklist covering:
1. Project setup
2. Security setup
3. Database build
4. Record type build
5. Constants and reference data
6. Expression rules and query rules
7. Interfaces
8. Process models
9. Record actions and related actions
10. Integrations and Web APIs
11. Dashboards and reports
12. Site configuration
13. Security verification
14. Testing
15. Deployment readiness
16. Final build acceptance

Apply the global Appian LLM instruction.
```

## Prompt 10: Create end-to-end test script

```text
Create an Appian end-to-end test script for the application or feature below.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Target Appian Version: 26.4
Feature or Application Scope: <SCOPE>
Roles: <ROLES>
Key Processes: <PROCESSES>

Return test scenarios with:
- scenario ID
- scenario name
- actor
- preconditions
- test data
- steps
- expected results
- actual results placeholder
- pass/fail placeholder
- defect reference placeholder

Cover happy path, validation errors, cancel path, approval path, rejection path, security, integration errors and reporting.
Apply the global Appian LLM instruction.
```

## Prompt 11: Review Appian SAIL code

```text
Review the supplied Appian SAIL code against the local Appian engineering library.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Target Appian Version: 26.4

Review for:
1. Non-Appian syntax contamination.
2. Invented SAIL components.
3. Invented component parameters.
4. Incorrect allowed values, such as bold instead of STRONG where applicable.
5. Unsafe saveInto patterns.
6. Missing null safety.
7. Query-in-loop patterns.
8. Refresh behaviour issues.
9. Mobile and accessibility issues.
10. Naming prefix issues.

Return a severity table with issue, location, why it matters and corrected Appian pattern.
Apply the global Appian LLM instruction.
```

## Prompt 12: Review Appian process model design

```text
Review the supplied Appian process model design against APPIAN_PROCESS_MODEL_DESIGN_BEST_PRACTICES.md.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Target Appian Version: 26.4

Review for:
1. Appian-native process model components.
2. Trigger clarity.
3. Process variables and parameter flags.
4. Rule input mapping.
5. Gateway logic.
6. Write Records nodes.
7. Parent-child write order.
8. Integration success and failure paths.
9. Exception handling.
10. Alerts and data management.
11. Security.
12. Tests.
13. Naming prefix consistency.

Return a severity table with corrected Appian process design patterns.
Apply the global Appian LLM instruction.
```

## Prompt 13: Review Appian database scripts

```text
Review the supplied database scripts for an Appian application.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Database Prefix: <database_prefix>
Target Appian Version: 26.4

Review for:
1. Table naming convention.
2. Primary key strategy.
3. Foreign key strategy.
4. Reference data design.
5. Audit fields.
6. Soft delete strategy.
7. Version or optimistic locking where needed.
8. Index strategy.
9. Seed data safety.
10. Migration and rollback risk.
11. Record type mapping suitability.

Return a severity table with corrected SQL or design recommendations.
Apply the global Appian LLM instruction.
```

## Prompt 14: Review full Appian application design

```text
Review the supplied Appian application design against the local Appian engineering library.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Database Prefix: <database_prefix>
Target Appian Version: 26.4

Review the design across:
1. Architecture completeness.
2. Data model and record types.
3. Constants and reference data.
4. Expression rules and query rules.
5. SAIL interfaces.
6. Process models.
7. Record actions and related actions.
8. Integrations and Web APIs.
9. Security.
10. Reporting and dashboards.
11. Error handling and audit.
12. Build execution sequence.
13. Testing.
14. Deployment and rollback.

Return:
- executive summary
- critical gaps
- detailed findings table
- recommended corrections
- readiness rating
Apply the global Appian LLM instruction.
```

## Prompt 15: Generate documentation from existing Appian code

```text
Generate Appian technical documentation from the supplied existing code and object inventory.

Application Name: <APPLICATION_NAME>
Application Short Name / Prefix: <PREFIX>
Database Prefix: <database_prefix>
Target Appian Version: 26.4
Existing Objects: <OBJECT_INVENTORY>
Code or Exported Snippets: <CODE>

Return:
1. Application/module overview
2. Object inventory summary
3. Data model and record type mapping
4. Expression rule summary
5. Interface summary
6. Process model summary
7. Integration summary
8. Security summary
9. Known issues and assumptions
10. Recommended refactoring backlog
11. Tests that should be added

Do not invent missing objects. Mark unknowns clearly.
Apply the global Appian LLM instruction.
```
