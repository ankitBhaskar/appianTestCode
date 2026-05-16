# Appian Engineering Design Engine

## Table of contents

1. [Purpose](#purpose)
2. [What this documentation set is](#what-this-documentation-set-is)
3. [What this documentation set is not](#what-this-documentation-set-is-not)
4. [How to use these files](#how-to-use-these-files)
5. [Recommended loading order](#recommended-loading-order)
6. [File index](#file-index)
7. [Source of truth](#source-of-truth)

## Purpose

This folder contains a project-agnostic Appian engineering design engine. It captures practical architecture rules, specification-writing standards, naming conventions, database design principles, SAIL guardrails, process model patterns, security standards and AI review checklists.

The intent is to help teams produce implementation-ready Appian specifications that are consistent, reviewable and safe for AI-assisted development.

## What this documentation set is

This is a reusable engineering baseline for Appian projects. It explains how to think through a feature from database design to record type mapping, expression rules, process models, interfaces, security, deployment and testing.

It is designed to be loaded into AI context alongside a project-specific kit, user stories, design notes and existing Appian code snippets.

## What this documentation set is not

This is not a project context file. Project-specific prefixes, business terms, record types, groups, integrations, data models and user journeys belong in a separate project kit.

This is not a replacement for official Appian documentation. Where Appian behaviour matters, official documentation remains the source of truth.

This is not copied from any other project specification. The wording, structure and examples are tailored to the APN engineering style used in this repository.

## How to use these files

Use these files as a design and review framework.

For a new feature, start with the methodology file, confirm the project kit, design the data model, map Appian objects, then write the specification in dependency order.

For AI-assisted work, provide these files plus the current project kit and ask the AI to follow the standards, not to copy examples literally.

## Recommended loading order

1. `00_Core_Methodology.md`
2. `01_Naming_and_Object_Standards.md`
3. `02_Database_Record_and_Data_Model_Standards.md`
4. `03_SAIL_Interface_Standards.md`
5. `04_Process_Model_and_Action_Standards.md`
6. `05_Security_Integration_and_Deployment_Standards.md`
7. `06_AI_Spec_Review_Checklists.md`
8. Project-specific kit and user stories

## File index

| File | Purpose |
|---|---|
| `00_Core_Methodology.md` | Explains how to use the engine, how to write build-ready Appian specs, and how to work with AI safely. |
| `01_Naming_and_Object_Standards.md` | Defines APN-style naming standards for Appian objects, database objects and release artefacts. |
| `02_Database_Record_and_Data_Model_Standards.md` | Covers database-first design, Appian record mapping, relationships, reference data and migration standards. |
| `03_SAIL_Interface_Standards.md` | Covers SAIL syntax, local variables, saveInto patterns, grids, null safety and common interface mistakes. |
| `04_Process_Model_and_Action_Standards.md` | Covers process model structure, start forms, record actions, related actions, write patterns and process testing. |
| `05_Security_Integration_and_Deployment_Standards.md` | Covers groups, record security, integration design, deployment sequencing and governance. |
| `06_AI_Spec_Review_Checklists.md` | Provides AI-safe generation rules, review prompts, quality gates and build checklist patterns. |

## Source of truth

Official Appian documentation remains the source of truth:

- https://docs.appian.com/suite/help/26.4/
- https://docs.appian.com/suite/help/26.4/Appian_Functions.html
- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/Records.html
- https://docs.appian.com/suite/help/26.4/Appian_Administration_Console.html
