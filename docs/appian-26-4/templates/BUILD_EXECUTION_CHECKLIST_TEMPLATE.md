# Appian Build Execution Checklist Template

## Table of contents

1. [Purpose](#purpose)
2. [AI usage note](#ai-usage-note)
3. [Project details](#project-details)
4. [Build status legend](#build-status-legend)
5. [Phase 1: Project setup](#phase-1-project-setup)
6. [Phase 2: Security setup](#phase-2-security-setup)
7. [Phase 3: Database build](#phase-3-database-build)
8. [Phase 4: Record type build](#phase-4-record-type-build)
9. [Phase 5: Constants and reference data](#phase-5-constants-and-reference-data)
10. [Phase 6: Expression rules and query rules](#phase-6-expression-rules-and-query-rules)
11. [Phase 7: Interfaces](#phase-7-interfaces)
12. [Phase 8: Process models](#phase-8-process-models)
13. [Phase 9: Record actions and related actions](#phase-9-record-actions-and-related-actions)
14. [Phase 10: Integrations and Web APIs](#phase-10-integrations-and-web-apis)
15. [Phase 11: Dashboards and reports](#phase-11-dashboards-and-reports)
16. [Phase 12: Site configuration](#phase-12-site-configuration)
17. [Phase 13: Security verification](#phase-13-security-verification)
18. [Phase 14: Testing](#phase-14-testing)
19. [Phase 15: Deployment readiness](#phase-15-deployment-readiness)
20. [Final build acceptance](#final-build-acceptance)

## Purpose

This checklist turns an Appian application build blueprint into a practical execution tracker.

Use this checklist to confirm that database objects, record types, rules, interfaces, process models, integrations, security and tests have been completed in the correct dependency order.

## AI usage note

The LLM must replace `<PREFIX>` with the supplied application short name and `<database_prefix>` with the supplied database prefix.

Do not default to `APN` unless the project prefix is explicitly `APN`.

## Project details

| Field | Value |
|---|---|
| Application name | `<APPLICATION_NAME>` |
| Application short name / prefix | `<PREFIX>` |
| Database prefix | `<database_prefix>` |
| Target Appian version | `26.4` |
| Release | `<RELEASE>` |
| Build owner | `<OWNER>` |
| Date | `<DATE>` |

## Build status legend

| Status | Meaning |
|---|---|
| Not started | Work has not begun. |
| In progress | Build is underway. |
| Blocked | Dependency or issue prevents completion. |
| Built | Object has been created. |
| Unit tested | Object-level testing completed. |
| Ready for review | Ready for technical review. |
| Complete | Accepted and ready for package. |

## Phase 1: Project setup

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 1.1 | Confirm Project Kit is complete. | `<Owner>` | None | Not started | Application name, prefix, users, scope and constraints confirmed. |
| 1.2 | Confirm target Appian version. | `<Owner>` | None | Not started | Appian version documented. |
| 1.3 | Confirm application prefix. | `<Owner>` | Project Kit | Not started | `<PREFIX>` approved. |
| 1.4 | Confirm database prefix. | `<Owner>` | Project Kit | Not started | `<database_prefix>` approved. |
| 1.5 | Create Appian application container. | `<Owner>` | Prefix approved | Not started | Application exists in Appian Designer. |
| 1.6 | Create folder structure. | `<Owner>` | Application | Not started | Records, rules, interfaces, process models, integrations and constants folders exist. |

## Phase 2: Security setup

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 2.1 | Create base user groups. | `<Owner>` | Prefix approved | Not started | `<PREFIX>_GRP_AllUsers` and role groups created. |
| 2.2 | Create admin group. | `<Owner>` | Prefix approved | Not started | `<PREFIX>_GRP_AppAdmins` exists. |
| 2.3 | Create support group. | `<Owner>` | Prefix approved | Not started | `<PREFIX>_GRP_SupportUsers` exists if required. |
| 2.4 | Create integration users group. | `<Owner>` | Integration scope | Not started | `<PREFIX>_GRP_IntegrationUsers` exists if required. |
| 2.5 | Configure folder security. | `<Owner>` | Folders | Not started | Access restricted by role. |

## Phase 3: Database build

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 3.1 | Review DDL scripts. | `<Owner>` | Data model | Not started | Scripts reviewed and approved. |
| 3.2 | Create reference tables. | `<Owner>` | DDL review | Not started | Reference tables created. |
| 3.3 | Create parent entity tables. | `<Owner>` | DDL review | Not started | Parent tables created. |
| 3.4 | Create core transaction tables. | `<Owner>` | Parent/reference tables | Not started | Core tables created. |
| 3.5 | Create child tables. | `<Owner>` | Core tables | Not started | History, comments and document tables created. |
| 3.6 | Create integration/support tables. | `<Owner>` | Core tables | Not started | Error log and sync log tables created. |
| 3.7 | Create indexes. | `<Owner>` | Tables | Not started | Indexes exist for FK, status, owner and date filters. |
| 3.8 | Load seed/reference data. | `<Owner>` | Reference tables | Not started | Seed data loaded and validated. |

## Phase 4: Record type build

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 4.1 | Create record types for parent entities. | `<Owner>` | Tables | Not started | Records sync successfully. |
| 4.2 | Create record types for core transactions. | `<Owner>` | Tables | Not started | Records sync successfully. |
| 4.3 | Create record types for child tables. | `<Owner>` | Tables | Not started | Records sync successfully. |
| 4.4 | Configure record fields. | `<Owner>` | Record types | Not started | Field names and types confirmed. |
| 4.5 | Configure relationships. | `<Owner>` | Related record types | Not started | Relationship navigation works. |
| 4.6 | Configure record list. | `<Owner>` | Record types | Not started | Record list shows expected fields. |
| 4.7 | Configure record security. | `<Owner>` | Groups | Not started | Access verified by role. |

## Phase 5: Constants and reference data

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 5.1 | Create technical constants. | `<Owner>` | Groups | Not started | Page size, group constants and limits created. |
| 5.2 | Create reference data constants if required. | `<Owner>` | Reference data design | Not started | Constant values match approved list. |
| 5.3 | Validate reference data records. | `<Owner>` | Seed data | Not started | Active values appear in Appian queries. |

## Phase 6: Expression rules and query rules

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 6.1 | Create query rules. | `<Owner>` | Record types | Not started | Null, empty and happy path tests pass. |
| 6.2 | Create validation rules. | `<Owner>` | Record types/constants | Not started | Valid and invalid scenarios pass. |
| 6.3 | Create formatting rules. | `<Owner>` | UI needs | Not started | User-facing values display correctly. |
| 6.4 | Create mapping rules. | `<Owner>` | Integration/data needs | Not started | Mapping tests pass. |
| 6.5 | Create utility rules. | `<Owner>` | Business logic | Not started | Unit tests pass. |

## Phase 7: Interfaces

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 7.1 | Create dashboard interfaces. | `<Owner>` | Query rules | Not started | Dashboard loads with expected data. |
| 7.2 | Create create/edit forms. | `<Owner>` | Rules/constants | Not started | Submit, cancel and validation paths work. |
| 7.3 | Create approval/review forms. | `<Owner>` | Rules/constants | Not started | Decision paths validated. |
| 7.4 | Create record summary interfaces. | `<Owner>` | Record types/rules | Not started | Summary renders all required fields. |
| 7.5 | Create reusable child components. | `<Owner>` | UI patterns | Not started | Components render correctly in parent screens. |
| 7.6 | Validate mobile behaviour. | `<Owner>` | Interfaces | Not started | Key screens work on narrow screens. |
| 7.7 | Validate accessibility. | `<Owner>` | Interfaces | Not started | Labels, validation and keyboard behaviour checked. |

## Phase 8: Process models

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 8.1 | Create create process models. | `<Owner>` | Interfaces/record types | Not started | Start form, cancel and write paths work. |
| 8.2 | Create submit process models. | `<Owner>` | Record types/rules | Not started | Status and history writes work. |
| 8.3 | Create approval process models. | `<Owner>` | Approval interface | Not started | Each decision branch works. |
| 8.4 | Create cancel/withdraw process models. | `<Owner>` | Cancel form | Not started | Status and history update correctly. |
| 8.5 | Create admin process models. | `<Owner>` | Admin interfaces | Not started | Admin changes write correctly. |
| 8.6 | Create exception subprocesses. | `<Owner>` | Error handling design | Not started | Error log and alert behaviour works. |
| 8.7 | Configure alerts and data management. | `<Owner>` | Process models | Not started | Alerts and archive settings configured. |

## Phase 9: Record actions and related actions

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 9.1 | Configure record actions. | `<Owner>` | Process models | Not started | Actions launch correctly. |
| 9.2 | Configure related actions. | `<Owner>` | Process models | Not started | Context passes to process variables. |
| 9.3 | Configure action visibility. | `<Owner>` | Groups/security | Not started | Authorised users see actions only. |
| 9.4 | Test action execution. | `<Owner>` | Actions | Not started | Process starts and completes. |

## Phase 10: Integrations and Web APIs

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 10.1 | Create connected systems. | `<Owner>` | Credentials | Not started | Authentication works. |
| 10.2 | Create integration objects. | `<Owner>` | Connected systems | Not started | Success and error responses tested. |
| 10.3 | Create integration wrapper rules. | `<Owner>` | Integration response sample | Not started | Dictionary mapping is safe. |
| 10.4 | Create Web APIs if required. | `<Owner>` | Process/rules | Not started | Request and response tested. |
| 10.5 | Configure integration error logging. | `<Owner>` | Error log table | Not started | Errors logged and visible to support. |

## Phase 11: Dashboards and reports

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 11.1 | Create user dashboards. | `<Owner>` | Query rules | Not started | Role-specific dashboard works. |
| 11.2 | Create manager/approver dashboards. | `<Owner>` | Query rules | Not started | Pending work queues work. |
| 11.3 | Create admin dashboards. | `<Owner>` | Query rules | Not started | Admin views work. |
| 11.4 | Create support dashboards. | `<Owner>` | Error log | Not started | Exception queue works. |
| 11.5 | Validate performance. | `<Owner>` | Dashboards | Not started | Large data scenario tested. |

## Phase 12: Site configuration

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 12.1 | Create Appian site. | `<Owner>` | Core interfaces | Not started | Site exists. |
| 12.2 | Configure site pages. | `<Owner>` | Interfaces/actions | Not started | Pages and navigation work. |
| 12.3 | Configure site security. | `<Owner>` | Groups | Not started | Role access verified. |
| 12.4 | Validate mobile access. | `<Owner>` | Site | Not started | Site usable on mobile where required. |

## Phase 13: Security verification

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 13.1 | Test employee/requester access. | `<Owner>` | Security config | Not started | User sees only permitted content. |
| 13.2 | Test manager/approver access. | `<Owner>` | Security config | Not started | Approver sees correct work queue. |
| 13.3 | Test admin access. | `<Owner>` | Security config | Not started | Admin functions restricted. |
| 13.4 | Test unauthorised access. | `<Owner>` | Security config | Not started | Access denied as expected. |
| 13.5 | Test integration access. | `<Owner>` | Connected systems/Web APIs | Not started | Service access controlled. |

## Phase 14: Testing

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 14.1 | Run expression rule tests. | `<Owner>` | Rules | Not started | All tests pass. |
| 14.2 | Run interface tests. | `<Owner>` | Interfaces | Not started | Load, validation, submit and cancel pass. |
| 14.3 | Run process model tests. | `<Owner>` | Processes | Not started | All gateway branches pass. |
| 14.4 | Run integration tests. | `<Owner>` | Integrations | Not started | Success and error paths pass. |
| 14.5 | Run end-to-end tests. | `<Owner>` | Full build | Not started | Business scenarios pass. |
| 14.6 | Run regression tests. | `<Owner>` | Existing app | Not started | No existing behaviour broken. |

## Phase 15: Deployment readiness

| Step | Work item | Owner | Dependency | Status | Validation |
|---:|---|---|---|---|---|
| 15.1 | Create deployment package. | `<Owner>` | Build complete | Not started | Package includes all objects. |
| 15.2 | Prepare database scripts. | `<Owner>` | DDL tested | Not started | Scripts ordered and reviewed. |
| 15.3 | Prepare import customisation file. | `<Owner>` | Environment values | Not started | Values provided for target environment. |
| 15.4 | Prepare rollback plan. | `<Owner>` | Release design | Not started | Rollback steps documented. |
| 15.5 | Run deployment rehearsal. | `<Owner>` | Package | Not started | Import succeeds in lower environment. |
| 15.6 | Complete release approval. | `<Owner>` | Testing complete | Not started | Approval captured. |

## Final build acceptance

| Acceptance item | Pass or fail | Notes |
|---|---|---|
| Build completed in dependency order. |  |  |
| All required objects created. |  |  |
| Security verified. |  |  |
| Unit tests passed. |  |  |
| End-to-end tests passed. |  |  |
| Deployment package created. |  |  |
| Rollback plan documented. |  |  |
| Business owner sign-off received. |  |  |
