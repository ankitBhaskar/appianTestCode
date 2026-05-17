# Appian Project Kit Template

## Table of contents

1. [Purpose](#purpose)
2. [How to use this template](#how-to-use-this-template)
3. [AI usage note](#ai-usage-note)
4. [Project identity](#project-identity)
5. [Business context](#business-context)
6. [Scope](#scope)
7. [Users, roles and security groups](#users-roles-and-security-groups)
8. [Business capabilities](#business-capabilities)
9. [Data model inputs](#data-model-inputs)
10. [Record type inputs](#record-type-inputs)
11. [Lifecycle and status model](#lifecycle-and-status-model)
12. [Reference data](#reference-data)
13. [Process model inputs](#process-model-inputs)
14. [Interface and UX inputs](#interface-and-ux-inputs)
15. [Integration inputs](#integration-inputs)
16. [Reporting inputs](#reporting-inputs)
17. [Security and governance inputs](#security-and-governance-inputs)
18. [Non-functional requirements](#non-functional-requirements)
19. [Environment and deployment inputs](#environment-and-deployment-inputs)
20. [Existing Appian object inventory](#existing-appian-object-inventory)
21. [Known constraints](#known-constraints)
22. [Open questions](#open-questions)
23. [LLM instruction block](#llm-instruction-block)

## Purpose

This template captures the project-specific context an LLM needs before generating Appian application documentation, build blueprints, technical specifications or code review findings.

Complete this Project Kit before asking an LLM to generate a full Appian app plan. The LLM must use this file as the source of truth for the application name, prefix, groups, entities, business process and constraints.

## How to use this template

Copy this file into the project folder and fill in the placeholders.

Recommended location for project-specific copies:

```text
/docs/<project-name>/PROJECT_KIT.md
```

Do not leave critical values blank when asking for build-ready output. At minimum, provide the application name, application short name, database prefix, target Appian version, users and scope.

## AI usage note

The application short name controls Appian object naming.

The LLM must not default to `APN` unless the project prefix is explicitly `APN`.

If this Project Kit says:

```text
Application Short Name / Prefix: LMS
```

then generated Appian objects should use names such as:

```text
LMS_REC_LeaveRequest
LMS_UI_CreateLeaveRequest
LMS_QRY_GetLeaveRequestsForEmployee
LMS_PM_CreateLeaveRequest
LMS_GRP_Employees
```

## Project identity

| Field | Value |
|---|---|
| Project name | `<PROJECT_NAME>` |
| Application name | `<APPLICATION_NAME>` |
| Application short name / Appian prefix | `<PREFIX>` |
| Database prefix | `<database_prefix>` |
| Target Appian version | `26.4` |
| Primary delivery phase | `<Phase 1 / MVP / Release 1>` |
| Business owner | `<BUSINESS_OWNER>` |
| Product owner | `<PRODUCT_OWNER>` |
| Technical owner | `<TECHNICAL_OWNER>` |
| Delivery team | `<TEAM_NAME>` |

## Business context

### Business problem

```text
<Describe the business problem the Appian application solves.>
```

### Business outcome

```text
<Describe the outcome expected after implementation.>
```

### Current process summary

```text
<Describe how the process works today, including manual steps, spreadsheets, emails or legacy systems.>
```

### Future process summary

```text
<Describe how the process should work once Appian is implemented.>
```

## Scope

### In scope

| Scope item | Description | Priority |
|---|---|---|
| `<Capability 1>` | `<Description>` | Must have |
| `<Capability 2>` | `<Description>` | Should have |

### Out of scope

| Item | Reason | Possible future phase |
|---|---|---|
| `<Out of scope item>` | `<Reason>` | `<Future phase>` |

### Assumptions

| Assumption | Impact if wrong | Owner to confirm |
|---|---|---|
| `<Assumption>` | `<Impact>` | `<Owner>` |

## Users, roles and security groups

### User roles

| Role | Description | Key responsibilities |
|---|---|---|
| `<Role 1>` | `<Description>` | `<Responsibilities>` |
| `<Role 2>` | `<Description>` | `<Responsibilities>` |

### Appian groups

Use the supplied application prefix.

| Group name | Purpose | Members | Notes |
|---|---|---|---|
| `<PREFIX>_GRP_AllUsers` | Base application access group. | `<Users>` | `<Notes>` |
| `<PREFIX>_GRP_AppAdmins` | Application administration and support. | `<Users>` | `<Notes>` |
| `<PREFIX>_GRP_<Role>` | `<Purpose>` | `<Users>` | `<Notes>` |
| `<PREFIX>_GRP_IntegrationUsers` | Integration service access. | `<Service accounts>` | Optional. |

### Security principles

```text
<Define important access rules, such as users only see their own records, managers see team records, HR admins see all records, or support users see exception queues.>
```

## Business capabilities

| Capability | Business description | Primary role | Expected Appian objects |
|---|---|---|---|
| `<Capability>` | `<Description>` | `<Role>` | Record, interface, process model, rules |

## Data model inputs

### Core business entities

| Entity | Description | Example fields | Notes |
|---|---|---|---|
| `<Entity>` | `<Description>` | `<Fields>` | `<Notes>` |

### Candidate database tables

Use the database prefix in table names.

| Table name | Purpose | Parent/child | Notes |
|---|---|---|---|
| `<database_prefix>_<entity>` | `<Purpose>` | Parent | `<Notes>` |
| `<database_prefix>_<entity>_status_history` | Status history. | Child | `<Notes>` |
| `<database_prefix>_<entity>_comment` | Comments. | Child | `<Notes>` |
| `<database_prefix>_<entity>_document` | Document metadata. | Child | `<Notes>` |

### Data retention and audit

| Area | Requirement |
|---|---|
| Audit fields | `<created_by, created_on, updated_by, updated_on, etc.>` |
| Soft delete | `<Yes / No / Required for selected tables>` |
| Retention | `<Retention requirement>` |
| Sensitive data | `<Sensitive fields and masking requirements>` |

## Record type inputs

| Record type | Source table | Purpose | Browsable? | Expected actions |
|---|---|---|---|---|
| `<PREFIX>_REC_<Entity>` | `<database_prefix>_<entity>` | `<Purpose>` | Yes/No | Create, edit, submit, approve |

### Required record relationships

| Base record type | Relationship name | Related record type | Relationship type | Field mapping |
|---|---|---|---|---|
| `<PREFIX>_REC_<Entity>` | `<relationshipName>` | `<PREFIX>_REC_<RelatedEntity>` | Many-to-one | `<field mapping>` |

## Lifecycle and status model

### Statuses

| Status code | Label | Description | Terminal? |
|---|---|---|---|
| DRAFT | Draft | Record created but not submitted. | No |
| SUBMITTED | Submitted | Record submitted for review. | No |
| APPROVED | Approved | Record approved. | Yes/No |
| REJECTED | Rejected | Record rejected. | Yes |
| CANCELLED | Cancelled | Record cancelled. | Yes |

### Status transitions

| From status | To status | Trigger | Actor | Process model |
|---|---|---|---|---|
| DRAFT | SUBMITTED | Submit action | Requester | `<PREFIX>_PM_Submit<Entity>` |
| SUBMITTED | APPROVED | Approve action | Approver | `<PREFIX>_PM_Approve<Entity>` |

## Reference data

| Reference data | Storage pattern | Example values | Admin maintained? |
|---|---|---|---|
| `<Reference>` | Reference table / constant | `<Values>` | Yes/No |

Recommended approach:

```text
Use reference tables for governed business values used in reporting, integration or admin screens. Use constants for stable technical configuration such as default page size or admin group references.
```

## Process model inputs

| Process model | Trigger | Purpose | Main actors | Notes |
|---|---|---|---|---|
| `<PREFIX>_PM_Create<Entity>` | Record action / site action | Create record. | Requester | Start form, Write Records. |
| `<PREFIX>_PM_Submit<Entity>` | Related action | Submit for review. | Requester | Status update, history write. |
| `<PREFIX>_PM_Approve<Entity>` | Related action / user task | Approve or reject. | Approver | Decision gateway. |
| `<PREFIX>_PM_Cancel<Entity>` | Related action | Cancel record. | Requester/Admin | Cancel form. |

### Process rules

```text
<Describe approval rules, assignment logic, escalation rules, SLA rules and exception handling.>
```

## Interface and UX inputs

| Interface | Purpose | Primary user | Device considerations |
|---|---|---|---|
| `<PREFIX>_UI_<Entity>Dashboard` | Dashboard for user role. | `<Role>` | Desktop/mobile |
| `<PREFIX>_UI_Create<Entity>` | Create form. | `<Role>` | Mobile-friendly |
| `<PREFIX>_UI_<Entity>Summary` | Record summary. | `<Role>` | Desktop/mobile |
| `<PREFIX>_UI_Approve<Entity>` | Approval form. | `<Role>` | Desktop/mobile |

### UX requirements

| Requirement | Details |
|---|---|
| Mobile support | `<Required / Not required / Specific screens>` |
| Accessibility | `<WCAG target or internal standard>` |
| Portal safety | `<Required / Not required>` |
| Language/localisation | `<Required / Not required>` |
| Branding | `<Design guidance>` |

## Integration inputs

| Integration | Direction | System | Purpose | Required now? |
|---|---|---|---|---|
| `<PREFIX>_INT_<Purpose>` | Outbound | `<System>` | `<Purpose>` | Yes/No |
| `<PREFIX>_API_<Purpose>` | Inbound | `<System>` | `<Purpose>` | Yes/No |

### Integration contract availability

| Integration | Request sample available? | Response sample available? | Owner |
|---|---|---|---|
| `<Integration>` | Yes/No | Yes/No | `<Owner>` |

If request or response samples are missing, the LLM must not invent payload structure. It should mark the integration mapping as requiring verification.

## Reporting inputs

| Report/dashboard | Audience | Purpose | Filters | Data source |
|---|---|---|---|---|
| `<Dashboard>` | `<Role>` | `<Purpose>` | `<Filters>` | `<Records>` |

## Security and governance inputs

| Security layer | Requirement |
|---|---|
| Site access | `<Groups>` |
| Application object security | `<Groups>` |
| Folder security | `<Groups>` |
| Record type security | `<Rules>` |
| Record action visibility | `<Rules>` |
| Process start security | `<Groups>` |
| Document folder security | `<Groups>` |
| Web API security | `<Authentication / authorisation>` |
| Connected system security | `<Credential handling>` |

## Non-functional requirements

| Area | Requirement |
|---|---|
| Performance | `<Expected volume, response time, dashboard needs>` |
| Availability | `<Availability target>` |
| Auditability | `<Audit requirements>` |
| Accessibility | `<Accessibility requirement>` |
| Privacy | `<Sensitive data requirements>` |
| Scalability | `<Future growth>` |
| Maintainability | `<Admin and support needs>` |

## Environment and deployment inputs

| Environment | Purpose | Notes |
|---|---|---|
| DEV | Development | `<Notes>` |
| TEST | System testing | `<Notes>` |
| UAT | User acceptance testing | `<Notes>` |
| PROD | Production | `<Notes>` |

### Deployment requirements

| Area | Requirement |
|---|---|
| Deployment package naming | `<PREFIX>_REL_<Version>_<Purpose>` |
| Database scripts | `<Required scripts>` |
| Import customisation file | `<Required / Not required>` |
| Rollback | `<Rollback strategy>` |
| Release approval | `<Approval process>` |

## Existing Appian object inventory

| Object name | Object type | Purpose | Reuse / modify / replace |
|---|---|---|---|
| `<Object>` | Record type / interface / rule / process | `<Purpose>` | Reuse |

## Known constraints

| Constraint | Impact | Mitigation |
|---|---|---|
| `<Constraint>` | `<Impact>` | `<Mitigation>` |

## Open questions

| Question | Owner | Required before build? | Notes |
|---|---|---|---|
| `<Question>` | `<Owner>` | Yes/No | `<Notes>` |

## LLM instruction block

Use this block when submitting the Project Kit to an LLM.

```text
Use this Project Kit as the project-specific source of truth.
Use the Application Short Name / Prefix supplied in this file for all Appian object names.
Use the Database Prefix supplied in this file for all table names.
Do not default to APN unless APN is explicitly supplied as the project prefix.
Do not invent Appian functions, SAIL components, parameters, process capabilities, record fields, groups, tables or integrations.
If required information is missing, ask targeted questions or mark assumptions clearly.
Generate Appian-native documentation using SAIL, Appian Expression Language and Appian process model concepts only.
```
