# Appian 26.x Best-Practices Knowledge Base Schema

## Purpose

This file is the authoring standard for every file under `docs/appian-26-4/best-practices/`. It exists so a human or LLM can generate, review and refactor Appian guidance consistently.

## Required YAML frontmatter

```yaml
---
title: <human-readable title>
file_id: <filename without .md>
topic: <one of the approved topics>
appian_version: 26.4
last_reviewed: 2026-05
tags: [<5-12 lowercase retrieval tags>]
applies_to: [<artefact types governed by the file>]
related_files: [<related file_ids>]
---
```

## Required section order

Every topic file must use this exact order:

1. Purpose
2. Scope
3. Core Principles
4. Detailed Best Practices
5. Decision Matrix
6. Worked End-to-End Example
7. Code Review Checklist
8. Common Failure Modes in Production
9. References

## Required best-practice structure

Every best practice must use this exact structure:

```text
### BP-<file_id>-<NN>: <Imperative rule title>

**Rule.** <One imperative sentence.>

**Rationale.** <4-8 sentences covering production impact.>

**How to apply.**
1. <Step>
2. <Step>
3. <Step>
4. <Step>

**Quantitative guidance.** <Numbers and thresholds.>

**Code example — GOOD.**
```sail
<valid Appian 26.x syntax>
```

**Code example — BAD.**
```sail
<anti-pattern with comments>
```

**Detection heuristics.**
- <Regex or structural signal>

**Exceptions.** <Precise exception rule.>

**Related practices.** <BP references.>
```

## Style rules

- Use plain Australian English.
- Use imperative voice.
- Do not use client, agency, programme, customer, proprietary system, real URL, real person, real environment or real schema names.
- Use neutral domains only: employee, customer, product, order, asset, claim, case, application and reference data.
- Use fenced code blocks with language hints.
- Use Appian terminology exactly: record type, record action, record data source, record sync, source filter, related record, process model, sub-process, MNI, interface, expression rule, integration, connected system, data store entity, CDT, constant, document, folder, group, user, role map, security summary, web API, custom output, plug-in and smart service.

## Quantitative anchors

Use these anchors unless a project-specific architecture decision overrides them:

| Area | Standard |
|---|---|
| Interface locals | Keep top-level `local!` variables under 30. |
| Rendered components | Keep immediately rendered components under 50. |
| Grid default paging | Use `batchSize: 25`. |
| Client-side row cap | Do not render more than 500 rows client-side. |
| Background query practical cap | Do not exceed 4000 rows without an approved export pattern. |
| Process model size | Keep process models under 50 nodes. |
| MNI batch size | Use 50-200 items per batch. |
| Process archive | Archive after 7 days unless audit requires more. |
| Integration timeout | Use 10 seconds connect timeout and 30 seconds read timeout for synchronous user-facing calls. |
| Async integration timeout | Use up to 120 seconds only for background work. |
| Integration retry | Retry up to 3 times with 1s, 2s and 4s backoff where idempotent. |
| Stored procedure commit batch | Commit 500-1000 rows per transaction. |
| Loop query threshold | Never query inside `a!forEach()` when the loop can exceed 5 items. |

## Version note standard

Use this format for Appian 26.x changes:

```text
> **Version note (26.x):** <what changed, what to use now, and what to avoid from earlier versions>
```

## Review rule

A file is compliant only when it has complete frontmatter, the required section order, all best-practice subsections, neutral examples, quantitative guidance and a code review checklist with at least 10 yes/no checks.