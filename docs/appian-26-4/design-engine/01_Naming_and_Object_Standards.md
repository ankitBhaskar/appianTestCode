# 01 Naming and Object Standards

## Table of contents

1. [Purpose](#purpose)
2. [Prefix rule](#prefix-rule)
3. [Database naming](#database-naming)
4. [Appian object naming](#appian-object-naming)
5. [Rule and variable naming](#rule-and-variable-naming)
6. [Group naming](#group-naming)
7. [Package and release naming](#package-and-release-naming)
8. [Anti-patterns](#anti-patterns)
9. [References](#references)

## Purpose

This file defines the APN-style naming standards used for Appian engineering artefacts.

These are recommended house standards. They are not Appian-enforced platform rules.

## Prefix rule

Every reusable Appian object should start with the application prefix.

Example prefix used in this repository:

```text
APN
```

Recommended pattern:

```text
APN_<ObjectType>_<BusinessPurpose>
```

Examples:

```text
APN_UI_ClaimSummary
APN_QRY_GetClaimById
APN_PM_CreateClaim
APN_REC_Claim
APN_INT_GetCustomer
```

## Database naming

### Tables

Use lowercase snake case for physical table names.

| Table type | Pattern | Example |
|---|---|---|
| Business table | `apn_<entity>` | `apn_claim` |
| Reference table | `apn_ref_<reference>` | `apn_ref_claim_status` |
| Integration table | `apn_int_<purpose>` | `apn_int_error_log` |
| Junction table | `apn_<entity>_<entity>` | `apn_case_tag` |
| History table | `apn_<entity>_history` | `apn_claim_status_history` |

### Columns

| Column type | Pattern | Example |
|---|---|---|
| Primary key | `<entity>_id` | `claim_id` |
| Foreign key | `<parent_entity>_id` | `customer_id` |
| Boolean | `is_<meaning>` or `has_<meaning>` | `is_active` |
| Date and time | `<event>_on` | `created_on` |
| User | `<event>_by` | `created_by` |
| Status code | `<entity>_status_code` | `claim_status_code` |

## Appian object naming

| Object | Pattern | Example |
|---|---|---|
| Record type | `APN_REC_<Entity>` | `APN_REC_Claim` |
| Interface | `APN_UI_<Purpose>` | `APN_UI_ClaimSummary` |
| Expression rule, query | `APN_QRY_<Verb><Entity>` | `APN_QRY_GetClaimById` |
| Expression rule, validation | `APN_VAL_<Purpose>` | `APN_VAL_IsClaimEditable` |
| Expression rule, mapping | `APN_MAP_<Source>To<Target>` | `APN_MAP_ClaimDtoToRecord` |
| Expression rule, format | `APN_FMT_<Purpose>` | `APN_FMT_DisplayCurrency` |
| Utility rule | `APN_UTIL_<Purpose>` | `APN_UTIL_IsNullOrEmpty` |
| Process model | `APN_PM_<BusinessProcess>` | `APN_PM_CreateClaim` |
| Integration | `APN_INT_<Verb><SystemOrEntity>` | `APN_INT_GetCustomer` |
| Connected system | `APN_CS_<System>` | `APN_CS_PaymentGateway` |
| Web API | `APN_API_<Verb><Entity>` | `APN_API_CreateClaim` |
| Constant | `APN_CONS_<Purpose>` | `APN_CONS_DefaultPageSize` |
| CDT | `APN_CDT_<Entity>` | `APN_CDT_Claim` |
| Data store | `APN_DS_<Domain>` | `APN_DS_Claims` |

## Rule and variable naming

Use lower camel case for local variables, rule inputs and process variables.

Examples:

```text
ri!claimId
ri!claimRecord
local!selectedStatus
local!activeClaims
pv!claimRecord
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
local!claimSummary
local!selectedDocumentType
pv!approvalDecision
```

## Group naming

Recommended pattern:

```text
APN_GRP_<RoleOrPurpose>
```

Examples:

```text
APN_GRP_Admins
APN_GRP_CaseManagers
APN_GRP_ReadOnlyUsers
APN_GRP_IntegrationUsers
APN_GRP_SupportUsers
```

## Package and release naming

Recommended release naming:

```text
APN_REL_<Version>_<Purpose>
```

Examples:

```text
APN_REL_1.0.0_InitialClaimsRelease
APN_REL_1.0.1_HotfixClaimValidation
APN_REL_1.1.0_PaymentEnhancements
```

## Anti-patterns

| Avoid | Prefer |
|---|---|
| `Rule1` | `APN_QRY_GetClaimById` |
| `TestInterface` | `APN_UI_ClaimSearch` |
| `NewProcessModel` | `APN_PM_AssessClaim` |
| `claimData` | `apn_claim` |
| `id` | `claim_id` |
| `status` with free text values | `claim_status_code` with reference table |
| `UserGroup1` | `APN_GRP_CaseManagers` |

## References

Official Appian 26.4 documentation remains the source of truth:

- https://docs.appian.com/suite/help/26.4/Appian_Objects.html
- https://docs.appian.com/suite/help/26.4/Records.html
