# Appian Naming Standards

## Table of contents

1. [Purpose](#purpose)
2. [Core naming principle](#core-naming-principle)
3. [Application prefix naming](#application-prefix-naming)
4. [Folder naming](#folder-naming)
5. [Constant naming](#constant-naming)
6. [Expression rule naming](#expression-rule-naming)
7. [Interface naming](#interface-naming)
8. [Process model naming](#process-model-naming)
9. [Process variable naming](#process-variable-naming)
10. [Rule input naming](#rule-input-naming)
11. [Local variable naming](#local-variable-naming)
12. [Record type naming](#record-type-naming)
13. [Record field naming](#record-field-naming)
14. [CDT naming](#cdt-naming)
15. [Data store naming](#data-store-naming)
16. [Integration naming](#integration-naming)
17. [Connected system naming](#connected-system-naming)
18. [Web API naming](#web-api-naming)
19. [Group naming](#group-naming)
20. [Package and release naming](#package-and-release-naming)
21. [Examples and anti-patterns](#examples-and-anti-patterns)
22. [References](#references)

## Purpose

This document defines recommended Appian object naming standards. These are house standards, not Appian-enforced platform rules.

## Core naming principle

Recommendation: every Appian object name should start with the application prefix, followed by the object type or purpose prefix.

For this knowledge base, `APN` is used as the example application prefix.

Pattern:

```text
<APP_PREFIX>_<OBJECT_TYPE>_<BusinessPurpose>
```

Examples:

```text
APN_UI_ClaimSummary
APN_QRY_GetClaimById
APN_PM_CreateClaim
APN_REC_Claim
APN_INT_GetCustomer
```

## Application prefix naming

| Item | Recommendation | Example |
|---|---|---|
| Application prefix | Three to five uppercase letters | `APN` |
| Domain application | Prefix plus domain name | `APN_Claims` |
| Shared application | Prefix plus shared domain | `APN_Shared` |
| Integration application | Prefix plus integration domain | `APN_Integrations` |

## Folder naming

Recommended folder names should also start with the application prefix where practical.

```text
APN_01_Admin
APN_02_Rules
APN_03_Interfaces
APN_04_Process_Models
APN_05_Record_Types
APN_06_Integrations
APN_07_Security
APN_08_Deployment
```

## Constant naming

Pattern:

```text
APN_CONS_<Purpose>
```

Examples:

```text
APN_CONS_DefaultPageSize
APN_CONS_MaxRetryCount
APN_CONS_SupportGroup
```

## Expression rule naming

| Rule Type | Recommended Pattern | Example |
|---|---|---|
| Query rule | `APN_QRY_<Verb><Entity>` | `APN_QRY_GetClaimById` |
| Validation rule | `APN_VAL_<ValidationPurpose>` | `APN_VAL_IsValidClaimStatus` |
| Mapping rule | `APN_MAP_<Source>To<Target>` | `APN_MAP_ClaimDtoToRecord` |
| Formatting rule | `APN_FMT_<Purpose>` | `APN_FMT_DisplayCurrency` |
| Utility rule | `APN_UTIL_<Purpose>` | `APN_UTIL_IsNullOrEmpty` |

Example:

```appian
rule!APN_QRY_GetClaimById(claimId: ri!claimId)
rule!APN_VAL_IsValidClaimStatus(status: ri!status)
```

## Interface naming

Pattern:

```text
APN_UI_<Purpose>
```

Examples:

```text
APN_UI_ClaimSummary
APN_UI_CreateClaim
APN_UI_ClaimSearch
```

## Process model naming

Pattern:

```text
APN_PM_<BusinessProcess>
```

Examples:

```text
APN_PM_CreateClaim
APN_PM_AssessClaim
APN_PM_ProcessPayment
```

## Process variable naming

Recommendation: use lower camel case and align the variable name to business meaning.

Examples:

```text
claimId
claimRecord
approvalDecision
integrationResponse
```

Avoid including the application prefix in process variables because they are scoped within the process model.

## Rule input naming

Recommendation: use lower camel case and clear business meaning.

Examples:

```text
claimId
customerId
pagingInfo
isReadOnly
```

## Local variable naming

Recommendation: use lower camel case and avoid vague names.

Examples:

```appian
local!claim
local!activeClaims
local!selectedStatus
local!validationMessage
```

## Record type naming

Pattern:

```text
APN_REC_<Entity>
```

Examples:

```text
APN_REC_Claim
APN_REC_Customer
APN_REC_Payment
```

## Record field naming

Recommendation: use lower camel case for record fields and align to database column meaning.

Examples:

```text
claimId
claimStatus
createdOn
createdBy
updatedOn
updatedBy
```

## CDT naming

Pattern:

```text
APN_CDT_<Entity>
```

Examples:

```text
APN_CDT_Claim
APN_CDT_Customer
APN_CDT_PaymentRequest
```

## Data store naming

Pattern:

```text
APN_DS_<Domain>
```

Examples:

```text
APN_DS_Claims
APN_DS_ReferenceData
```

## Integration naming

Pattern:

```text
APN_INT_<Verb><SystemOrEntity>
```

Examples:

```text
APN_INT_GetCustomer
APN_INT_CreatePayment
APN_INT_SearchEmployer
```

## Connected system naming

Pattern:

```text
APN_CS_<ExternalSystem>
```

Examples:

```text
APN_CS_PaymentGateway
APN_CS_CustomerRegistry
```

## Web API naming

Pattern:

```text
APN_API_<Verb><Entity>
```

Examples:

```text
APN_API_CreateClaim
APN_API_GetClaimStatus
APN_API_UpdateEmployer
```

## Group naming

Pattern:

```text
APN_GRP_<RoleOrPurpose>
```

Examples:

```text
APN_GRP_Admins
APN_GRP_CaseManagers
APN_GRP_ReadOnlyUsers
APN_GRP_IntegrationUsers
```

## Package and release naming

Pattern:

```text
APN_REL_<ReleaseVersion>_<Purpose>
```

Examples:

```text
APN_REL_1.0.0_InitialClaimsRelease
APN_REL_1.0.1_HotfixClaimValidation
```

## Examples and anti-patterns

Avoid:

```text
Rule1
TestInterface
NewProcessModel
QRY_GetActiveClaims
UI_ClaimSearch
PM_Claims_Assessment
```

Prefer:

```text
APN_QRY_GetActiveClaims
APN_UI_ClaimSearch
APN_PM_AssessClaim
APN_REC_Claim
APN_INT_GetCustomer
```

## References

Official Appian documentation:

- https://docs.appian.com/suite/help/26.4/
- https://docs.appian.com/suite/help/26.4/Appian_Objects.html
