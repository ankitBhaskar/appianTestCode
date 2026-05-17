# LLM App Build Plan Router

## Purpose

This short router tells an LLM which local repository files to load when the user asks for a complete Appian application build plan.

Use this file when the request sounds like:

```text
Create a leave tracking and management system.
Create an Appian app for supplier onboarding.
Design a full claims management application.
Give me a development execution plan for a case management system.
```

## Required behaviour

When the user asks for a complete Appian application or major module, the LLM must produce a full development execution plan, not only a summary.

The plan must include:

1. Application definition
2. Scope and assumptions
3. User roles and security groups
4. Business capability map
5. Data model design
6. Database DDL execution plan
7. Record type design
8. Constants and reference data
9. Expression rules and query rules
10. SAIL interface design
11. Process model design
12. Record actions and related actions
13. Integrations and Web APIs
14. Reporting and dashboards
15. Error handling and audit design
16. Appian application structure
17. Build execution sequence
18. Unit test plan
19. End-to-end test script
20. Deployment and release plan

## Mandatory files to load

Load these local repository files before generating the plan:

```text
docs/appian-26-4/design-engine/LLM_USAGE_GUIDE_FOR_APPIAN_DOCUMENTATION.md
docs/appian-26-4/design-engine/APPIAN_APPLICATION_BUILD_BLUEPRINT.md
docs/appian-26-4/design-engine/01_Naming_and_Object_Standards.md
docs/appian-26-4/design-engine/AI_REVIEW_GUARDRAILS_APPIAN.md
docs/appian-26-4/design-engine/02_Database_Record_and_Data_Model_Standards.md
docs/appian-26-4/design-engine/03_SAIL_Interface_Standards.md
docs/appian-26-4/design-engine/APPIAN_PROCESS_MODEL_DESIGN_BEST_PRACTICES.md
docs/appian-26-4/design-engine/APPIAN_PATTERN_LIBRARY.md
docs/appian-26-4/Appian_Functions/functions.md
docs/appian-26-4/Appian_Functions/sail-components.md
docs/appian-26-4/Appian_Functions/sail-recipes-ux-best-practices.md
```

## Required project input

The LLM must look for this block in the user prompt:

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

If the application short name is missing, the LLM must ask for it or use `<PREFIX>` placeholders. It must not default to `APN` unless the supplied prefix is `APN`.

## Standard command template

```text
Create a full Appian development execution plan for:

Application Name: Leave Management System
Application Short Name / Prefix: LMS
Database Prefix: lms
Target Appian Version: 26.4
Users and Roles: Employee, Manager, HR Admin, App Admin
Scope: leave request, approval, leave balance, dashboards, reporting and administration
Integrations: optional HR employee sync and payroll notification
Mobile Requirements: employees can submit leave from mobile
Reporting Requirements: employee dashboard, manager approval queue, HR leave utilisation dashboard

Use the local Appian engineering library.
Return the full 20-section build blueprint.
Do not use Java, JavaScript, React, HTML or CSS inside SAIL.
Do not invent Appian functions, components, parameters, record fields, groups or process capabilities.
```

## Quality gate

Before returning the plan, the LLM must confirm internally:

| Gate | Requirement |
|---|---|
| Prefix gate | All objects use the supplied prefix. |
| Completeness gate | All 20 blueprint sections are present. |
| Data-first gate | Tables and record types are defined before interfaces. |
| Appian-native gate | Process models use Appian nodes and SAIL uses Appian components. |
| Execution gate | Build order is dependency-aware. |
| Testing gate | Unit and end-to-end tests are provided. |
| Security gate | Groups, record security, action visibility and process security are included. |
| Deployment gate | Release, migration and rollback are included. |
