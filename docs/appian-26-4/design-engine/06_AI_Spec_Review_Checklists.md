# 06 AI Specification Review Checklists

## Table of contents

1. [Purpose](#purpose)
2. [AI generation rules](#ai-generation-rules)
3. [Pre-generation checklist](#pre-generation-checklist)
4. [Specification completeness checklist](#specification-completeness-checklist)
5. [SAIL safety checklist](#sail-safety-checklist)
6. [Database and record checklist](#database-and-record-checklist)
7. [Process model checklist](#process-model-checklist)
8. [Integration checklist](#integration-checklist)
9. [Final review prompt](#final-review-prompt)
10. [References](#references)

## Purpose

This file provides review checklists for AI-assisted Appian specification generation.

The goal is to prevent common AI mistakes such as invented parameters, invalid SAIL components, weak null safety, incomplete object inventories and unsupported assumptions.

## AI generation rules

| Rule | Standard |
|---|---|
| Use official documentation | Verify Appian functions, components and parameters against official Appian 26.4 documentation. |
| Do not invent | Never invent object features, compatibility flags or platform limits. |
| Use project context | Use the project prefix, groups and record types from the project kit. |
| Label recommendations | Clearly label house standards as recommendations. |
| Generate complete specs | Do not write “same as above” for objects that need build detail. |
| Keep examples safe | Use fabricated sample data and avoid real personal information. |

## Pre-generation checklist

| Question | Answer required |
|---|---|
| What is the application prefix? |  |
| What user story or feature is being built? |  |
| What record types already exist? |  |
| What tables already exist? |  |
| What groups control access? |  |
| Are there integrations? |  |
| Is a sample integration payload available? |  |
| What environments are in scope? |  |
| What is out of scope? |  |

## Specification completeness checklist

| Section | Required |
|---|---|
| Header and purpose | Yes |
| Story references | Yes |
| Design decisions | Yes |
| Dependencies | Yes |
| Object inventory | Yes |
| DDL | Yes, where database changes exist |
| Record type mapping | Yes |
| Expression rules | Yes |
| Interfaces | Yes |
| Process models | Yes |
| Actions | Yes |
| Security | Yes |
| Integration and wiring | Where applicable |
| Unit tests | Yes |
| End-to-end checklist | Yes |
| End-to-end test script | Yes |
| Deployment notes | Yes |

## SAIL safety checklist

| Check | Pass or fail |
|---|---|
| Interface uses `ri!`, not `pv!`. |  |
| Process model notes use `pv!`, not `ri!`. |  |
| Local variables are declared clearly. |  |
| Inputs used in filters are null-guarded. |  |
| Dates are formatted with null checks. |  |
| Query rules specify required fields. |  |
| `a!sortInfo()` is inside `a!pagingInfo()`. |  |
| Save behaviour uses a working copy and submit write-back. |  |
| Dropdowns use documented parameters. |  |
| Button styles are documented Appian values. |  |
| Grids avoid unsupported parameters. |  |
| Record action links use supported Appian components. |  |

## Database and record checklist

| Check | Pass or fail |
|---|---|
| Table names use the project prefix. |  |
| Primary keys are meaningful. |  |
| Foreign keys are documented. |  |
| Reference data is governed. |  |
| Audit fields are present. |  |
| Soft delete behaviour is defined. |  |
| Indexes match expected filters and joins. |  |
| Record type primary key is documented. |  |
| Record type display field is documented. |  |
| Relationships are named and mapped. |  |
| Security is documented. |  |

## Process model checklist

| Check | Pass or fail |
|---|---|
| Process has a clear trigger. |  |
| Process variables are documented. |  |
| Start form parameters are marked correctly. |  |
| Cancel path is explicit. |  |
| Write Records sequencing is correct. |  |
| Audit fields are stamped. |  |
| Alerts are configured. |  |
| Data management setting is defined. |  |
| Unit tests are included. |  |

## Integration checklist

| Check | Pass or fail |
|---|---|
| Connected system is named. |  |
| Authentication approach is documented. |  |
| Request payload is documented. |  |
| Response payload is documented. |  |
| Wrapper rule handles dictionaries safely. |  |
| Success path is documented. |  |
| Error path is documented. |  |
| Retry and idempotency are considered. |  |
| Logging is defined. |  |

## Final review prompt

Use this prompt to review an AI-generated Appian specification:

```text
Review this Appian specification against the APN design engine.
Identify:
1. Any invented Appian functions, components or parameters.
2. Any missing object descriptions.
3. Any missing DDL, record type, expression rule, interface or process details.
4. Any unsafe saveInto, null handling or query patterns.
5. Any missing security, deployment or rollback notes.
6. Any areas where the spec makes assumptions without evidence.
7. Any sections that need to be rewritten before a developer can build from it.
Return findings as a review table with severity, issue, location and recommended correction.
```

## References

Official Appian 26.4 documentation remains the source of truth:

- https://docs.appian.com/suite/help/26.4/
- https://docs.appian.com/suite/help/26.4/Appian_Functions.html
- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/Process_Modeling.html
