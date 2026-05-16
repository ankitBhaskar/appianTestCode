# 00 Core Methodology

## Table of contents

1. [Purpose](#purpose)
2. [Audience](#audience)
3. [Core principle](#core-principle)
4. [Working pipeline](#working-pipeline)
5. [AI-assisted design rules](#ai-assisted-design-rules)
6. [Specification authoring standard](#specification-authoring-standard)
7. [Build order](#build-order)
8. [Required specification sections](#required-specification-sections)
9. [Quality bar](#quality-bar)
10. [References](#references)

## Purpose

This file defines the core methodology for producing Appian engineering specifications that are build-ready, reviewable and suitable for AI-assisted development.

It is project-agnostic. Project-specific content belongs in a separate project kit.

## Audience

This file supports three audiences:

| Audience | How they use it |
|---|---|
| Architects | Use it to shape consistent feature designs and review implementation decisions. |
| Developers | Use it as the delivery standard for Appian specifications and builds. |
| AI assistants | Use it as a guardrail for generating Appian artefacts without inventing unsupported syntax or project context. |

## Core principle

Do not design by assumption.

A good Appian specification is created by verifying the data model, dependencies, users, security, integration contracts and build sequence before writing code. If a design decision can go multiple ways, document the options and choose deliberately.

## Working pipeline

Recommended delivery flow:

```text
Business notes
  -> user stories
  -> data model decisions
  -> Appian object inventory
  -> technical specification
  -> build
  -> test
  -> release
```

### Business notes

Capture business language, operational rules, edge cases, pain points and known constraints. Do not treat raw notes as final implementation design.

### User stories

Translate notes into clear user stories with roles, actions and outcomes. Link each story to acceptance criteria and delivery scope.

### Data model decisions

Confirm the database shape before building interfaces. Appian record types, relationships, queries and process writes depend on the data model.

### Technical specification

Create one build-ready specification per feature or coherent work package. It should include DDL, record types, expression rules, process models, interfaces, integration points, testing and deployment notes.

### Build

Develop in dependency order. Do not start with a SAIL form before the data model and record type are understood.

## AI-assisted design rules

When using AI to help with Appian delivery, apply these rules.

| Rule | Standard |
|---|---|
| Confirm context | Provide project prefix, groups, record types, data model and user stories before asking for detailed code. |
| Avoid invention | Do not accept invented Appian functions, components, parameters or compatibility flags. Verify against official documentation. |
| Ask for missing artefacts | If an integration response, existing interface or current data model is required, request it before generating dependent code. |
| Separate standards from facts | Label recommended house standards separately from Appian-enforced platform behaviour. |
| Use project prefix | Examples should use the current project prefix, such as `APN`, unless the project kit states otherwise. |
| Keep examples generic | Do not import another project domain into the current project. |

## Specification authoring standard

Recommended file format:

```text
<Feature_Name>_Technical_Specification.md
```

A specification must be readable in GitHub, diffable in pull requests and detailed enough for a developer to build without guessing.

## Build order

Use this order unless the project architecture has a documented reason to vary it.

```text
1. Database DDL and reference data
2. Record types and relationships
3. Constants and groups
4. Expression rules
5. Interfaces
6. Process models
7. Record actions and related actions
8. Site or portal wiring
9. Deployment package
10. Test execution
```

## Required specification sections

Every feature specification should include:

1. Header and purpose
2. Story references and scope
3. Design decisions
4. Dependencies
5. Object inventory
6. DDL and data migration scripts
7. Record type configuration
8. Constants and reference data
9. Expression rules
10. Interfaces
11. Process models
12. Record actions and related actions
13. Security model
14. Integration or wiring notes
15. Unit tests by layer
16. End-to-end implementation checklist
17. End-to-end test script
18. Deployment and rollback notes

## Quality bar

A specification is not complete until it can answer these questions:

| Question | Expected answer |
|---|---|
| What tables are created or changed? | DDL and migration files are documented. |
| What Appian objects are created or changed? | Object inventory is complete. |
| What order should the build follow? | Dependency order is explicit. |
| How is data secured? | Groups, record security and action visibility are documented. |
| How is data written? | Process models and Write Records behaviour are documented. |
| How is it tested? | Unit and end-to-end tests are included. |
| What could fail? | Error handling and rollback are documented. |

## References

Official Appian 26.4 documentation remains the source of truth:

- https://docs.appian.com/suite/help/26.4/
- https://docs.appian.com/suite/help/26.4/Records.html
- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/Process_Modeling.html
