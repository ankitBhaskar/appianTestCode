# Appian Naming Standards

## Table of contents

1. Application naming
2. Folder naming
3. Expression rules
4. Interfaces
5. Process models
6. Record types
7. Integrations
8. Groups
9. Examples and anti-patterns

## Application naming

Recommendation:

| Object | Standard |
|---|---|
| Application Prefix | APP |
| Shared Application | SHARED |
| Integration Application | INT |

Example:

```text
APP_CLAIMS
APP_FINANCE
SHARED_COMMON
```

## Folder naming

Use functional domain separation.

```text
01_ADMIN
02_RULES
03_INTERFACES
04_PROCESSES
05_RECORDS
06_SECURITY
```

## Expression rules

| Rule Type | Prefix |
|---|---|
| Query | rule!QRY_ |
| Validation | rule!VAL_ |
| Mapping | rule!MAP_ |
| Formatting | rule!FMT_ |

Example:

```appian
rule!QRY_GetClaimById()
rule!VAL_IsValidClaim()
```

## Interfaces

Recommended prefix:

```text
UI_
```

Example:

```text
UI_ClaimSummary
UI_CreateClaim
```

## Process models

Recommended naming:

```text
PM_Claims_CreateClaim
PM_Claims_Assessment
```

## Record types

Recommended naming:

```text
RT_Claim
RT_Customer
```

## Integrations

Recommended naming:

```text
INT_GetCustomer
INT_CreatePayment
```

## Groups

Recommended naming:

```text
GRP_APP_ADMINS
GRP_CLAIMS_CASE_MANAGERS
```

## Examples and anti-patterns

Avoid:

```text
Rule1
TestInterface
NewProcessModel
```

Prefer:

```text
QRY_GetActiveClaims
UI_ClaimSearch
PM_Claims_Assessment
```

## References

Official Appian documentation:

- https://docs.appian.com/suite/help/26.4/
