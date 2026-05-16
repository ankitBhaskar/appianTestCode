# 01 Naming and Object Standards

## Table of contents

1. [Purpose](#purpose)
2. [Application prefix rule](#application-prefix-rule)
3. [How an LLM must resolve the prefix](#how-an-llm-must-resolve-the-prefix)
4. [Database naming](#database-naming)
5. [Appian object naming](#appian-object-naming)
6. [Rule and variable naming](#rule-and-variable-naming)
7. [Group naming](#group-naming)
8. [Package and release naming](#package-and-release-naming)
9. [Anti-patterns](#anti-patterns)
10. [References](#references)

## Purpose

This file defines the naming standards used for Appian engineering artefacts.

These are recommended house standards. They are not Appian-enforced platform rules.

Important: `APN` is the default/example prefix used in this documentation library. It is not mandatory for every project. The actual application short name or prefix must come from the project prompt or project kit.

## Application prefix rule

Every reusable Appian object should start with the application short name, also referred to as the application prefix.

The prefix must be provided to the LLM as project input.

Examples:

| Application name | Short name / prefix | Example object |
|---|---|---|
| User Management Solution | `UMS` | `UMS_UI_UserSummary` |
| Long Service Claims | `LSC` | `LSC_QRY_GetClaimById` |
| Payments Management | `PAY` | `PAY_PM_ApprovePayment` |
| Appian reference example | `APN` | `APN_REC_Claim` |

Recommended pattern:

```text
<PREFIX>_<ObjectType>_<BusinessPurpose>
```

Examples using a supplied prefix of `UMS`:

```text
UMS_UI_UserSummary
UMS_QRY_GetUserById
UMS_PM_CreateUser
UMS_REC_User
UMS_INT_GetEmployeeDetails
```

Examples using a supplied prefix of `LSC`:

```text
LSC_UI_ClaimSummary
LSC_QRY_GetClaimById
LSC_PM_CreateClaim
LSC_REC_Claim
LSC_INT_GetEmployerDetails
```

## How an LLM must resolve the prefix

The LLM must not blindly use `APN` unless the project prompt explicitly states that `APN` is the application prefix.

Prefix resolution order:

```text
1. Use the application short name or prefix explicitly provided in the prompt.
2. If the prompt provides an application name and short name, use the short name.
3. If the prompt provides only an application name, ask for the short name before generating build-ready object names.
4. If the user asks for draft examples only and no prefix is supplied, use <PREFIX> placeholders or clearly state that APN is only an example.
5. Never validate a project as incorrect merely because it does not use APN.
```

Required LLM behaviour:

| Situation | Required behaviour |
|---|---|
| Prompt says `Application short name: UMS` | Generate and validate names using `UMS_`. |
| Prompt says `Application name: User Management Solution, short name: UMS` | Use `UMS_` for Appian objects and `ums_` for database tables. |
| Prompt says `Use APN` | Use `APN_`. |
| Prompt gives no short name | Ask for the short name or use `<PREFIX>` placeholders. |
| Existing project uses another prefix | Follow the existing project prefix and flag inconsistent new objects. |

Standard project input block:

```text
Application Name: User Management Solution
Application Short Name / Prefix: UMS
Database Prefix: ums
Target Appian Version: 26.4
```

## Database naming

### Tables

Use lowercase snake case for physical table names. The database prefix should normally be the lowercase version of the application short name unless the project kit defines a different database prefix.

Pattern:

```text
<prefix>_<entity>
```

Examples using `UMS` / `ums`:

| Table type | Pattern | Example |
|---|---|---|
| Business table | `<prefix>_<entity>` | `ums_user` |
| Reference table | `<prefix>_ref_<reference>` | `ums_ref_user_status` |
| Integration table | `<prefix>_int_<purpose>` | `ums_int_error_log` |
| Junction table | `<prefix>_<entity>_<entity>` | `ums_user_role` |
| History table | `<prefix>_<entity>_history` | `ums_user_status_history` |

### Columns

| Column type | Pattern | Example |
|---|---|---|
| Primary key | `<entity>_id` | `user_id` |
| Foreign key | `<parent_entity>_id` | `organisation_id` |
| Boolean | `is_<meaning>` or `has_<meaning>` | `is_active` |
| Date and time | `<event>_on` | `created_on` |
| User | `<event>_by` | `created_by` |
| Status code | `<entity>_status_code` | `user_status_code` |

## Appian object naming

Use the supplied application prefix in place of `<PREFIX>`.

| Object | Pattern | UMS example |
|---|---|---|
| Record type | `<PREFIX>_REC_<Entity>` | `UMS_REC_User` |
| Interface | `<PREFIX>_UI_<Purpose>` | `UMS_UI_UserSummary` |
| Expression rule, query | `<PREFIX>_QRY_<Verb><Entity>` | `UMS_QRY_GetUserById` |
| Expression rule, validation | `<PREFIX>_VAL_<Purpose>` | `UMS_VAL_IsUserEditable` |
| Expression rule, mapping | `<PREFIX>_MAP_<Source>To<Target>` | `UMS_MAP_UserDtoToRecord` |
| Expression rule, format | `<PREFIX>_FMT_<Purpose>` | `UMS_FMT_DisplayUserStatus` |
| Utility rule | `<PREFIX>_UTIL_<Purpose>` | `UMS_UTIL_IsNullOrEmpty` |
| Process model | `<PREFIX>_PM_<BusinessProcess>` | `UMS_PM_CreateUser` |
| Integration | `<PREFIX>_INT_<Verb><SystemOrEntity>` | `UMS_INT_GetEmployeeDetails` |
| Connected system | `<PREFIX>_CS_<System>` | `UMS_CS_IdentityProvider` |
| Web API | `<PREFIX>_API_<Verb><Entity>` | `UMS_API_CreateUser` |
| Constant | `<PREFIX>_CONS_<Purpose>` | `UMS_CONS_DefaultPageSize` |
| CDT | `<PREFIX>_CDT_<Entity>` | `UMS_CDT_User` |
| Data store | `<PREFIX>_DS_<Domain>` | `UMS_DS_UserManagement` |

## Rule and variable naming

Use lower camel case for local variables, rule inputs and process variables.

Examples:

```text
ri!userId
ri!userRecord
local!selectedStatus
local!activeUsers
pv!userRecord
pv!isCancelled
```

Avoid vague names:

```text
local!data
local!item
local!temp
pv!x
```

Prefer meaningful names:

```text
local!userSummary
local!selectedRole
pv!approvalDecision
```

## Group naming

Recommended pattern:

```text
<PREFIX>_GRP_<RoleOrPurpose>
```

Examples using `UMS`:

```text
UMS_GRP_Admins
UMS_GRP_UserManagers
UMS_GRP_ReadOnlyUsers
UMS_GRP_IntegrationUsers
UMS_GRP_SupportUsers
```

## Package and release naming

Recommended release naming:

```text
<PREFIX>_REL_<Version>_<Purpose>
```

Examples using `UMS`:

```text
UMS_REL_1.0.0_InitialUserManagementRelease
UMS_REL_1.0.1_HotfixUserValidation
UMS_REL_1.1.0_RoleManagementEnhancements
```

## Anti-patterns

| Avoid | Prefer |
|---|---|
| Blindly using `APN_` for every project | Use the supplied project prefix, such as `UMS_`, `LSC_` or `PAY_`. |
| `Rule1` | `<PREFIX>_QRY_GetUserById` |
| `TestInterface` | `<PREFIX>_UI_UserSearch` |
| `NewProcessModel` | `<PREFIX>_PM_AssessUserRequest` |
| `userData` | `<prefix>_user` |
| `id` | `user_id` |
| `status` with free text values | `user_status_code` with reference table |
| `UserGroup1` | `<PREFIX>_GRP_UserManagers` |

## References

Official Appian 26.4 documentation remains the source of truth:

- https://docs.appian.com/suite/help/26.4/Appian_Objects.html
- https://docs.appian.com/suite/help/26.4/Records.html
