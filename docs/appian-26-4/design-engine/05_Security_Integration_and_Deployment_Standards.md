# 05 Security, Integration and Deployment Standards

## Table of contents

1. [Purpose](#purpose)
2. [Security principles](#security-principles)
3. [Group architecture](#group-architecture)
4. [Record and action security](#record-and-action-security)
5. [Integration standards](#integration-standards)
6. [Web API standards](#web-api-standards)
7. [Deployment standards](#deployment-standards)
8. [Governance checklist](#governance-checklist)
9. [References](#references)

## Purpose

This file defines APN standards for Appian security, integration design and deployment governance.

## Security principles

| Principle | Standard |
|---|---|
| Least privilege | Users should only receive the access needed for their role. |
| Defence in depth | Do not rely on UI hiding alone. Combine object, record, action and process security. |
| Group-based control | Use role-based groups rather than individual user exceptions where practical. |
| Support traceability | Production support access must be controlled and auditable. |
| Environment separation | Development, test and production access must be reviewed separately. |

## Group architecture

Recommended groups:

```text
APN_GRP_AllUsers
APN_GRP_Admins
APN_GRP_CaseManagers
APN_GRP_ReadOnlyUsers
APN_GRP_Approvers
APN_GRP_IntegrationUsers
APN_GRP_SupportUsers
```

Use separate groups for materially different roles. Avoid using one large group plus interface-only conditions for security.

## Record and action security

Every record type should define:

| Area | Required detail |
|---|---|
| View security | Who can see the record type. |
| Create security | Who can create records. |
| Edit security | Who can update records. |
| Delete or deactivate security | Who can remove or soft-delete records. |
| Record action visibility | Which groups can see each action. |
| Process start security | Which groups can launch backing processes. |

## Integration standards

Before writing integration wrapper rules, obtain a sample request and response payload.

Integration specification must include:

```text
Integration name
Connected system
Method
Purpose
Authentication approach
Request body or query parameters
Response example
Success behaviour
Error behaviour
Timeout or retry approach
Logging approach
Security owner
```

For POST integrations, clearly state whether the POST is read-only search or a state-changing operation.

## Web API standards

Web API specifications must include:

| Area | Required detail |
|---|---|
| Endpoint purpose | What business operation the API supports. |
| Authentication | How callers authenticate. |
| Authorisation | Which callers are permitted. |
| Request contract | Required and optional fields. |
| Response contract | Success and error responses. |
| Idempotency | Whether repeated calls are safe. |
| Error handling | How validation, business and system errors are returned. |
| Logging | What is logged and where. |

## Deployment standards

Deployment package should include:

```text
Appian package
Import customisation file
Database scripts
Reference data scripts
Post-import configuration notes
Smoke test script
Rollback or remediation plan
```

Recommended promotion flow:

```mermaid
flowchart LR
  DEV[Development] --> TST[Test]
  TST --> UAT[User Acceptance Testing]
  UAT --> PRD[Production]
```

## Governance checklist

| Check | Complete |
|---|---|
| Group model reviewed. |  |
| Record type security reviewed. |  |
| Record actions have visibility rules. |  |
| Process start security aligns with action visibility. |  |
| Integration credentials are not hardcoded. |  |
| Web APIs have authentication and authorisation. |  |
| Database migration scripts are version controlled. |  |
| Import customisation file is prepared. |  |
| Production validation checklist is complete. |  |

## References

Official Appian 26.4 documentation remains the source of truth:

- https://docs.appian.com/suite/help/26.4/Appian_Administration_Console.html
- https://docs.appian.com/suite/help/26.4/Connected_System_Object.html
- https://docs.appian.com/suite/help/26.4/Integration_Object.html
- https://docs.appian.com/suite/help/26.4/Web_APIs.html
- https://docs.appian.com/suite/help/26.4/Managing_Application_Deployments.html
