# Appian 26.4 SAIL Components Engineering Reference

## Table of contents

1. [Purpose](#purpose)
2. [Source of truth](#source-of-truth)
3. [AI generation rule](#ai-generation-rule)
4. [Official component inventory groups](#official-component-inventory-groups)
5. [Responsive design rules](#responsive-design-rules)
6. [Form layout standards](#form-layout-standards)
7. [Columns and side-by-side layout standards](#columns-and-side-by-side-layout-standards)
8. [Pane layout standards](#pane-layout-standards)
9. [Section and card standards](#section-and-card-standards)
10. [Input component standards](#input-component-standards)
11. [Button and action standards](#button-and-action-standards)
12. [Rich text and icon standards](#rich-text-and-icon-standards)
13. [Grid and list standards](#grid-and-list-standards)
14. [Chart standards](#chart-standards)
15. [Picker standards](#picker-standards)
16. [Accessibility standards](#accessibility-standards)
17. [Mobile and portal safety standards](#mobile-and-portal-safety-standards)
18. [Common AI mistakes](#common-ai-mistakes)
19. [Component review checklist](#component-review-checklist)
20. [References](#references)

## Purpose

This document provides APN engineering guidance for using Appian 26.4 SAIL components safely.

It is designed to prevent AI-generated SAIL from using unsupported components, random parameters, web-framework values, CSS values, or invalid Appian allowed values.

It is not a full replacement for the official Appian Interface Components page.

## Source of truth

Use official Appian documentation for exact component syntax, parameter names, allowed values, compatibility notes and examples.

Key sources:

- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/SAIL_Recipes.html
- https://docs.appian.com/suite/help/26.4/interface-performance.html
- https://docs.appian.com/suite/help/26.4/sail/ux-styled-icons.html
- https://docs.appian.com/suite/help/26.4/Chart_Color_Scheme.html
- https://docs.appian.com/suite/help/26.4/reference-translation-strings.html
- https://docs.appian.com/suite/help/26.4/refresh-behavior-interfaces.html

## AI generation rule

```text
Use SAIL components only.
Do not use HTML tags, React components, CSS classes, JavaScript event handlers, Tailwind classes, or invented Appian parameters.
Every component, parameter and allowed value must be supported by Appian 26.4.
```

## Official component inventory groups

The official Interface Components page is the canonical inventory. The APN documentation organises components into review groups.

| Group | Engineering purpose | Review focus |
|---|---|---|
| Layouts | Page structure, forms, columns, panes, cards and sections. | Responsive behaviour, nesting and content width. |
| Inputs | Capture user data. | `value`, `saveInto`, `required`, `readOnly`, validation and refresh behaviour. |
| Display components | Present read-only information. | Null-safe display, accessibility and empty states. |
| Action components | Submit, cancel, navigate, launch actions or save local state. | Correct action pattern, security and validation behaviour. |
| Grids and lists | Present tabular or repeated data. | Paging, sorting, field selection and mobile behaviour. |
| Charts | Display summaries and trends. | Colour schemes, labels, empty states and drill-through. |
| Pickers | Search and select users, groups, records or custom values. | Suggestion rules, selected labels and performance. |
| Browsers | Browse larger object sets where supported. | Usability and security. |
| Rich text | Styled text, links and icons. | Allowed style values and accessible meaning. |
| Document components | Upload and access documents. | Folder security, file limits and process mapping. |
| Navigation components | Link users to records, pages and external resources. | Safe links, record links and mobile navigation. |

## Responsive design rules

Appian interface design should assume different screen sizes.

Recommended APN standards:

| Area | Standard |
|---|---|
| Page width | Prefer balanced content width for most site pages. Avoid unnecessarily stretched layouts. |
| Task forms | Prefer narrow or medium content width where suitable. |
| Columns | Use columns for major page structure. Expect stacking on narrow screens. |
| Side-by-side layouts | Use for compact inline grouping, not major page structure. |
| Mobile pages | Avoid forcing desktop-style wide grids or panes onto phones. |
| Navigation | Keep mobile-first site navigation concise where practical. |
| Buttons | Ensure action layout remains usable when flattened on phones. |

## Form layout standards

Use forms for create, edit, review and submit flows.

Recommended form structure:

```appian
a!localVariables(
  local!workingRecord: ri!record,

  a!formLayout(
    titleBar: a!headerTemplateSimple(
      title: "Create Claim",
      secondaryText: "Capture claim details"
    ),
    contents: {
      a!sectionLayout(
        label: "Claim Details",
        contents: {
          a!textField(
            label: "Claim Reference",
            value: local!workingRecord.claimReference,
            saveInto: local!workingRecord.claimReference,
            required: true
          )
        }
      )
    },
    buttons: a!buttonLayout(
      primaryButtons: {
        a!buttonWidget(
          label: "Submit",
          style: "SOLID",
          submit: true,
          saveInto: a!save(ri!record, local!workingRecord),
          validate: true
        )
      },
      secondaryButtons: {
        a!buttonWidget(
          label: "Cancel",
          style: "OUTLINE",
          submit: true,
          saveInto: a!save(ri!isCancelled, true),
          validate: false
        )
      }
    )
  )
)
```

Review checks:

| Check | Required outcome |
|---|---|
| Form uses supported title/header pattern. | Yes. |
| Contents are grouped into clear sections. | Yes. |
| Buttons use documented styles and validation behaviour. | Yes. |
| Form fields save to a working copy. | Yes. |
| Submit writes back to rule inputs. | Yes. |

## Columns and side-by-side layout standards

Use columns for high-level responsive structure.

Use side-by-side layout for compact fields or label/value groupings.

Review standard:

```text
Do not use CSS flexbox, HTML divs or React layout components.
Use Appian layout components only.
```

Safe pattern:

```appian
a!columnsLayout(
  columns: {
    a!columnLayout(
      contents: {
        a!textField(label: "First Name")
      }
    ),
    a!columnLayout(
      contents: {
        a!textField(label: "Last Name")
      }
    )
  }
)
```

## Pane layout standards

Use pane layouts only where the use case genuinely needs a multi-pane experience.

Review checks:

| Check | Required outcome |
|---|---|
| Phone behaviour considered. | Yes. |
| List/detail pattern is suitable. | Yes. |
| Content is not hidden behind complex panes. | Yes. |
| Alternative mobile behaviour is considered. | Yes. |

## Section and card standards

Use sections for semantic grouping.

Use cards for visual grouping, summaries, KPIs or contextual panels.

Recommendation:

- use true section labels and headings rather than rich text that only looks like a heading
- keep card content focused
- avoid nesting many card layers
- provide meaningful empty states

## Input component standards

For every input component, confirm exact parameter support in the official docs.

Common expected design items:

| Input area | Standard |
|---|---|
| Label | Required unless the design intentionally collapses it with accessible context. |
| Value | Bound to local working value. |
| saveInto | Saves to local working value first. |
| Required | Used for mandatory data. |
| Read-only | Used for view or locked state. |
| Validation | Business rules shown clearly. |
| Placeholder | Used only where supported and helpful. |
| Refresh behaviour | Avoid unnecessary refresh on each keystroke. |

### Text input

Use for short text.

Review focus:

- character limit
- required validation
- refresh behaviour
- placeholder support
- read-only behaviour

### Paragraph input

Use for longer comments, explanations or notes.

Review focus:

- avoid using it for short codes
- keep height suitable for task flow
- consider maximum length and validation

### Dropdowns, radio buttons and checkboxes

Use controlled choices for governed values.

Recommended decision guide:

| Need | Preferred pattern |
|---|---|
| Many choices | Dropdown or picker. |
| Few mutually exclusive choices | Radio buttons where supported. |
| Multiple selections | Checkbox or multi-select component where supported. |
| Governed business status | Reference data query or constant-driven choices. |

## Button and action standards

Buttons should use Appian-supported action behaviour.

Reject:

```text
onClick
onSubmit
handleClick
href button hacks
JavaScript event handlers
```

Use Appian patterns:

```appian
a!buttonWidget(
  label: "Search",
  style: "SOLID",
  saveInto: {
    a!save(local!searchTriggered, true)
  }
)
```

For record actions, use Appian record action components and process mappings rather than manually simulating actions.

## Rich text and icon standards

Rich text must use Appian rich text components and documented style values.

Reject natural language or CSS-style values such as:

```text
bold
fontWeight
className
text-red-500
<span>
<b>
```

Use Appian rich text patterns:

```appian
a!richTextDisplayField(
  value: {
    a!richTextItem(
      text: "Important",
      style: "STRONG"
    )
  }
)
```

Review rule:

```text
If the style value is not listed for the specific Appian component, do not use it.
```

## Grid and list standards

Grids should be backed by query rules that select required fields only.

Grid review standards:

| Area | Standard |
|---|---|
| Data source | Query record types or safe local data. |
| Fields | Select only fields used by the grid. |
| Paging | Always consider page size and sort order. |
| Total count | Fetch only where needed. |
| Empty state | Provide meaningful message. |
| Actions | Keep row actions clear. |
| Mobile | Avoid very wide grids where mobile users are in scope. |

Example column:

```appian
a!gridColumn(
  label: "Claim Reference",
  value: fv!row[recordType!APN_REC_Claim.fields.claimReference],
  width: "MEDIUM"
)
```

## Chart standards

Charts should summarise data, not hide operational detail.

Review standards:

- use clear labels
- use an approved chart colour scheme
- include empty state handling
- avoid overloading charts with too many categories
- provide a grid or drill-through where users need details

## Picker standards

Picker components require careful performance review.

Review standards:

| Area | Standard |
|---|---|
| Suggestion source | Query only what is needed. |
| Selected labels | Render selected values clearly. |
| Security | Do not reveal unauthorised records in suggestions. |
| Performance | Avoid broad unfiltered searches. |
| Empty state | Provide useful guidance. |

## Accessibility standards

Accessibility is not optional.

Review standards:

| Area | Standard |
|---|---|
| Headings | Use real headings and section labels where possible. |
| Images and icons | Provide text equivalents where they communicate meaning. |
| Colour | Do not rely on colour alone. |
| Validation | Messages should explain what to fix. |
| Keyboard use | Avoid patterns that require mouse-only interaction. |
| Announcements | Use documented announcement behaviour for important messages where supported. |

## Mobile and portal safety standards

For mobile and portal contexts:

- confirm component compatibility
- keep navigation simple
- avoid desktop-only pane assumptions
- avoid wide grids as the primary mobile interaction
- keep task forms concise
- test with narrow screen behaviour

## Common AI mistakes

| Mistake | Correction |
|---|---|
| Using `bold` as a rich text style | Use documented Appian style values, such as `STRONG` where supported. |
| Using CSS properties like `fontWeight` | Use Appian component parameters only. |
| Using React/HTML components | Use SAIL components only. |
| Using unsupported button styles like `primary` without verification | Use only documented Appian button styles. |
| Using random width values copied from CSS | Use width values documented for the specific component. |
| Using unsupported grid parameters | Verify grid parameters in official docs. |
| Querying inside row rendering | Query once before grid rendering. |
| Building a desktop-only layout | Design for responsive behaviour. |
| Hiding security-sensitive actions only with UI conditions | Enforce security at action, process and record layers. |

## Component review checklist

| Check | Pass or fail |
|---|---|
| Every component exists in Appian 26.4 documentation. |  |
| Every named parameter belongs to that component. |  |
| Every allowed value uses Appian-supported wording and casing. |  |
| No HTML, React, CSS or JavaScript appears inside SAIL. |  |
| Inputs save into local working values. |  |
| Submit writes back to rule inputs. |  |
| Query-backed components avoid repeated queries. |  |
| Refresh behaviour is intentional. |  |
| Mobile behaviour has been considered. |  |
| Accessibility has been considered. |  |
| Portal compatibility is confirmed where relevant. |  |

## References

Official Appian documentation:

- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/SAIL_Recipes.html
- https://docs.appian.com/suite/help/26.4/interface-performance.html
- https://docs.appian.com/suite/help/26.4/sail/ux-styled-icons.html
- https://docs.appian.com/suite/help/26.4/Chart_Color_Scheme.html
- https://docs.appian.com/suite/help/26.4/reference-translation-strings.html
- https://docs.appian.com/suite/help/26.4/refresh-behavior-interfaces.html
