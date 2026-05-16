# Appian 26.4 SAIL Recipes and UX Best Practices

## Table of contents

1. [Purpose](#purpose)
2. [Source of truth and verification rule](#source-of-truth-and-verification-rule)
3. [AI-safe SAIL recipe rules](#ai-safe-sail-recipe-rules)
4. [Recipe 1: Standard create form](#recipe-1-standard-create-form)
5. [Recipe 2: Edit form with working copy](#recipe-2-edit-form-with-working-copy)
6. [Recipe 3: Read-only record summary](#recipe-3-read-only-record-summary)
7. [Recipe 4: Two-column responsive form](#recipe-4-two-column-responsive-form)
8. [Recipe 5: Search and results page](#recipe-5-search-and-results-page)
9. [Recipe 6: Grid with empty state](#recipe-6-grid-with-empty-state)
10. [Recipe 7: Grid row actions](#recipe-7-grid-row-actions)
11. [Recipe 8: Dependent dropdowns](#recipe-8-dependent-dropdowns)
12. [Recipe 9: Refresh-on-filter-change pattern](#recipe-9-refresh-on-filter-change-pattern)
13. [Recipe 10: Validation message pattern](#recipe-10-validation-message-pattern)
14. [Recipe 11: Status tag using rich text](#recipe-11-status-tag-using-rich-text)
15. [Recipe 12: KPI card row](#recipe-12-kpi-card-row)
16. [Recipe 13: Document upload form section](#recipe-13-document-upload-form-section)
17. [Recipe 14: Comments and notes section](#recipe-14-comments-and-notes-section)
18. [Recipe 15: Approval decision form](#recipe-15-approval-decision-form)
19. [Recipe 16: Mobile-safe layout pattern](#recipe-16-mobile-safe-layout-pattern)
20. [Recipe 17: User display pattern](#recipe-17-user-display-pattern)
21. [Recipe 18: Null-safe date display](#recipe-18-null-safe-date-display)
22. [Recipe 19: Integration response display pattern](#recipe-19-integration-response-display-pattern)
23. [Recipe 20: Reusable child component pattern](#recipe-20-reusable-child-component-pattern)
24. [UX best-practice checklist](#ux-best-practice-checklist)
25. [Performance checklist](#performance-checklist)
26. [Accessibility checklist](#accessibility-checklist)
27. [Mobile checklist](#mobile-checklist)
28. [Common AI mistakes in recipes](#common-ai-mistakes-in-recipes)
29. [References](#references)

## Purpose

This document provides APN-style SAIL recipes and UX best practices for Appian 26.4 interface design.

It is designed to help AI assistants and developers produce Appian-safe SAIL code without importing JavaScript, React, HTML, CSS, or unsupported parameter names into Appian interfaces.

The examples are practical patterns, not a replacement for the official Appian SAIL Recipes or Interface Components documentation.

## Source of truth and verification rule

Official Appian documentation remains the source of truth for exact component names, function names, parameters, allowed values, compatibility flags and examples.

Primary source:

- https://docs.appian.com/suite/help/26.4/SAIL_Recipes.html

Related sources:

- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/interface-performance.html
- https://docs.appian.com/suite/help/26.4/refresh-behavior-interfaces.html
- https://docs.appian.com/suite/help/26.4/sail/ux-styled-icons.html
- https://docs.appian.com/suite/help/26.4/Chart_Color_Scheme.html
- https://docs.appian.com/suite/help/26.4/reference-translation-strings.html

Verification rule:

```text
Before using any recipe in Appian Designer, confirm every SAIL component,
parameter and allowed value against the official Appian 26.4 documentation.
```

## AI-safe SAIL recipe rules

These rules apply to every recipe in this document.

| Rule | Standard |
|---|---|
| Appian only | Do not use JavaScript, React, HTML, CSS, Python or pseudo-code inside SAIL. |
| Exact components | Use only documented Appian SAIL components. |
| Exact parameters | Do not invent plausible parameter names. |
| Exact allowed values | Do not replace Appian values with natural language values such as `bold` or web values such as `primary`. |
| Working copy | Editable forms should normally save into a local working record and write back on submit. |
| Null safety | Guard optional values before display, comparison, indexing or formatting. |
| Query safety | Select required fields, use paging and avoid query-in-loop patterns. |
| Mobile safety | Avoid fixed desktop-only structures for user-facing forms. |
| Accessibility | Use labels, meaningful sections and non-colour-only status communication. |

## Recipe 1: Standard create form

### Use when

Use this pattern for a simple record creation action launched from a record action, related action, or process start form.

### Rule inputs

```text
ri!record        Record Type    Input/Output
ri!isCancelled   Boolean        Output
```

### SAIL pattern

```appian
a!localVariables(
  local!workingRecord: ri!record,

  a!formLayout(
    titleBar: a!headerTemplateSimple(
      title: "Create Record",
      secondaryText: "Enter the required details"
    ),
    contents: {
      a!sectionLayout(
        label: "Details",
        contents: {
          a!textField(
            label: "Reference",
            value: local!workingRecord.reference,
            saveInto: local!workingRecord.reference,
            required: true
          ),
          a!paragraphField(
            label: "Description",
            value: local!workingRecord.description,
            saveInto: local!workingRecord.description,
            required: true
          )
        }
      )
    },
    buttons: a!buttonLayout(
      primaryButtons: {
        a!buttonWidget(
          label: "Create",
          style: "SOLID",
          submit: true,
          saveInto: {
            a!save(local!workingRecord.createdBy, loggedInUser()),
            a!save(local!workingRecord.createdOn, now()),
            a!save(local!workingRecord.updatedBy, loggedInUser()),
            a!save(local!workingRecord.updatedOn, now()),
            a!save(ri!record, local!workingRecord)
          },
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

### UX notes

- Keep required fields visible and grouped.
- Use concise labels.
- Use instructions only where the user genuinely needs help.
- Do not overload a create form with unrelated detail sections.

## Recipe 2: Edit form with working copy

### Use when

Use this pattern when editing an existing record.

### SAIL pattern

```appian
a!localVariables(
  local!workingRecord: ri!record,
  local!isReadOnly: a!defaultValue(ri!isReadOnly, false),

  a!formLayout(
    titleBar: a!headerTemplateSimple(
      title: "Edit Record",
      secondaryText: "Update the details and submit changes"
    ),
    contents: {
      a!sectionLayout(
        label: "Record Details",
        contents: {
          a!textField(
            label: "Reference",
            value: local!workingRecord.reference,
            saveInto: local!workingRecord.reference,
            required: true,
            readOnly: local!isReadOnly
          ),
          a!paragraphField(
            label: "Notes",
            value: local!workingRecord.notes,
            saveInto: local!workingRecord.notes,
            readOnly: local!isReadOnly
          )
        }
      )
    },
    buttons: a!buttonLayout(
      primaryButtons: {
        a!buttonWidget(
          label: "Save",
          style: "SOLID",
          submit: true,
          saveInto: {
            a!save(local!workingRecord.updatedBy, loggedInUser()),
            a!save(local!workingRecord.updatedOn, now()),
            a!save(ri!record, local!workingRecord)
          },
          validate: true,
          showWhen: not(local!isReadOnly)
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

### UX notes

- Show the current record context in the title or first section.
- Use read-only mode where edits are not permitted.
- Avoid saving directly into complex record references from individual fields.

## Recipe 3: Read-only record summary

### Use when

Use this pattern for record views, summary pages, review screens and confirmation screens.

### SAIL pattern

```appian
a!localVariables(
  local!record: ri!record,

  {
    a!sectionLayout(
      label: "Summary",
      contents: {
        a!columnsLayout(
          columns: {
            a!columnLayout(
              contents: {
                a!textField(
                  label: "Reference",
                  value: a!defaultValue(local!record.reference, "-"),
                  readOnly: true
                ),
                a!textField(
                  label: "Status",
                  value: a!defaultValue(local!record.status, "-"),
                  readOnly: true
                )
              }
            ),
            a!columnLayout(
              contents: {
                a!textField(
                  label: "Created By",
                  value: rule!APN_FMT_DisplayUser(local!record.createdBy),
                  readOnly: true
                ),
                a!textField(
                  label: "Created On",
                  value: if(
                    isnull(local!record.createdOn),
                    "-",
                    text(local!record.createdOn, "dd/MM/yyyy")
                  ),
                  readOnly: true
                )
              }
            )
          }
        )
      }
    )
  }
)
```

### UX notes

- Read-only screens should not look like editable forms unless the fields are intentionally locked.
- Use clear section labels.
- Do not display raw usernames where a display-name rule exists.

## Recipe 4: Two-column responsive form

### Use when

Use this pattern where two related sets of fields can be placed side by side on desktop and stacked on smaller screens.

### SAIL pattern

```appian
a!columnsLayout(
  columns: {
    a!columnLayout(
      contents: {
        a!textField(
          label: "First Name",
          value: local!workingRecord.firstName,
          saveInto: local!workingRecord.firstName,
          required: true
        ),
        a!textField(
          label: "Email",
          value: local!workingRecord.email,
          saveInto: local!workingRecord.email
        )
      }
    ),
    a!columnLayout(
      contents: {
        a!textField(
          label: "Last Name",
          value: local!workingRecord.lastName,
          saveInto: local!workingRecord.lastName,
          required: true
        ),
        a!textField(
          label: "Phone",
          value: local!workingRecord.phone,
          saveInto: local!workingRecord.phone
        )
      }
    )
  }
)
```

### UX notes

- Keep related fields together.
- Avoid too many columns on data-entry forms.
- Confirm stacking behaviour in mobile testing.

## Recipe 5: Search and results page

### Use when

Use this pattern for operational search pages and work queues.

### SAIL pattern

```appian
a!localVariables(
  local!status,
  local!searchText,
  local!pagingInfo: a!pagingInfo(startIndex: 1, batchSize: 25),
  local!results: a!refreshVariable(
    value: rule!APN_QRY_SearchClaims(
      status: local!status,
      searchText: local!searchText,
      pagingInfo: local!pagingInfo
    ),
    refreshOnVarChange: local!pagingInfo
  ),

  {
    a!sectionLayout(
      label: "Search Filters",
      contents: {
        a!columnsLayout(
          columns: {
            a!columnLayout(
              contents: {
                a!textField(
                  label: "Search Text",
                  value: local!searchText,
                  saveInto: local!searchText
                )
              }
            ),
            a!columnLayout(
              contents: {
                a!dropdownField(
                  label: "Status",
                  choiceLabels: { "Open", "In Review", "Closed" },
                  choiceValues: { "OPEN", "IN_REVIEW", "CLOSED" },
                  value: local!status,
                  saveInto: local!status,
                  placeholder: "Any status"
                )
              }
            )
          }
        ),
        a!buttonLayout(
          primaryButtons: {
            a!buttonWidget(
              label: "Search",
              style: "SOLID",
              saveInto: a!save(
                local!results,
                rule!APN_QRY_SearchClaims(
                  status: local!status,
                  searchText: local!searchText,
                  pagingInfo: local!pagingInfo
                )
              )
            )
          },
          secondaryButtons: {
            a!buttonWidget(
              label: "Reset",
              style: "OUTLINE",
              saveInto: {
                a!save(local!status, null),
                a!save(local!searchText, null)
              }
            )
          }
        )
      }
    )
  }
)
```

### UX notes

- Do not load very large result sets on initial page load.
- Provide Reset where filters are complex.
- Keep filter labels aligned with business terms.

## Recipe 6: Grid with empty state

### Use when

Use this pattern for read-only search results or related record lists.

### SAIL pattern

```appian
if(
  length(local!results.data) = 0,
  a!richTextDisplayField(
    labelPosition: "COLLAPSED",
    value: a!richTextItem(
      text: "No records found.",
      style: "EMPHASIS"
    ),
    align: "CENTER"
  ),
  a!gridField(
    label: "Results",
    data: local!results.data,
    columns: {
      a!gridColumn(
        label: "Reference",
        value: fv!row[recordType!APN_REC_Claim.fields.claimReference]
      ),
      a!gridColumn(
        label: "Status",
        value: fv!row[recordType!APN_REC_Claim.fields.claimStatusCode]
      ),
      a!gridColumn(
        label: "Created On",
        value: if(
          isnull(fv!row[recordType!APN_REC_Claim.fields.createdOn]),
          "-",
          text(fv!row[recordType!APN_REC_Claim.fields.createdOn], "dd/MM/yyyy")
        )
      )
    },
    pageSize: 25
  )
)
```

### UX notes

- Use a helpful empty message.
- Do not show a blank grid without explanation.
- Keep grid columns to the minimum needed for the task.

## Recipe 7: Grid row actions

### Use when

Use this pattern where users need to act on records from a grid.

### SAIL pattern

```appian
a!gridColumn(
  label: "Actions",
  value: a!recordActionField(
    actions: {
      a!recordActionItem(
        action: recordType!APN_REC_Claim.actions.updateClaim,
        identifier: fv!row[recordType!APN_REC_Claim.fields.claimId]
      )
    }
  )
)
```

### UX notes

- Do not simulate record actions with JavaScript-style clicks.
- Ensure record action visibility and process start security are configured.
- Keep row actions limited and task-relevant.

## Recipe 8: Dependent dropdowns

### Use when

Use this pattern where the second dropdown depends on the first selection.

### SAIL pattern

```appian
a!localVariables(
  local!selectedCategory,
  local!selectedSubcategory,
  local!subcategoryOptions: a!refreshVariable(
    value: if(
      isnull(local!selectedCategory),
      {},
      rule!APN_QRY_GetSubcategories(categoryCode: local!selectedCategory)
    ),
    refreshOnVarChange: local!selectedCategory
  ),

  {
    a!dropdownField(
      label: "Category",
      choiceLabels: { "General", "Payment", "Service" },
      choiceValues: { "GENERAL", "PAYMENT", "SERVICE" },
      value: local!selectedCategory,
      saveInto: {
        a!save(local!selectedCategory, save!value),
        a!save(local!selectedSubcategory, null)
      },
      placeholder: "Select category"
    ),
    a!dropdownField(
      label: "Subcategory",
      choiceLabels: local!subcategoryOptions.label,
      choiceValues: local!subcategoryOptions.code,
      value: local!selectedSubcategory,
      saveInto: local!selectedSubcategory,
      placeholder: "Select subcategory",
      disabled: isnull(local!selectedCategory)
    )
  }
)
```

### UX notes

- Clear child selection when parent changes.
- Disable child dropdown until parent is selected.
- Do not query child options if parent value is null.

## Recipe 9: Refresh-on-filter-change pattern

### Use when

Use this pattern when results must refresh after a specific variable changes.

### SAIL pattern

```appian
a!localVariables(
  local!selectedStatus,
  local!records: a!refreshVariable(
    value: rule!APN_QRY_GetRecordsByStatus(status: local!selectedStatus),
    refreshOnVarChange: local!selectedStatus
  ),

  {
    a!dropdownField(
      label: "Status",
      choiceLabels: { "Open", "Closed" },
      choiceValues: { "OPEN", "CLOSED" },
      value: local!selectedStatus,
      saveInto: local!selectedStatus,
      placeholder: "Select status"
    )
  }
)
```

### UX notes

- Use refresh deliberately.
- Avoid expensive refresh on every typed character.
- Use a Search button where filters are complex or integrations are slow.

## Recipe 10: Validation message pattern

### Use when

Use this pattern for business validation beyond basic required fields.

### SAIL pattern

```appian
a!localVariables(
  local!workingRecord: ri!record,
  local!isInvalidAmount: and(
    not(isnull(local!workingRecord.amount)),
    local!workingRecord.amount <= 0
  ),

  a!decimalField(
    label: "Amount",
    value: local!workingRecord.amount,
    saveInto: local!workingRecord.amount,
    required: true,
    validations: if(
      local!isInvalidAmount,
      "Amount must be greater than zero.",
      null
    )
  )
)
```

### UX notes

- Validation should tell the user how to fix the issue.
- Avoid technical error messages.
- Keep validation near the field where possible.

## Recipe 11: Status tag using rich text

### Use when

Use this pattern for lightweight status display.

### SAIL pattern

```appian
a!richTextDisplayField(
  labelPosition: "COLLAPSED",
  value: {
    a!richTextItem(
      text: a!defaultValue(local!statusLabel, "Unknown"),
      style: "STRONG"
    )
  }
)
```

### UX notes

- Do not use unsupported values such as `bold`.
- Do not rely on colour alone to communicate status.
- Pair status labels with meaningful text.

## Recipe 12: KPI card row

### Use when

Use this pattern for dashboard summary metrics.

### SAIL pattern

```appian
a!columnsLayout(
  columns: {
    a!columnLayout(
      contents: {
        a!cardLayout(
          contents: {
            a!richTextDisplayField(
              labelPosition: "COLLAPSED",
              value: {
                a!richTextItem(
                  text: tostring(local!openCount),
                  size: "LARGE",
                  style: "STRONG"
                ),
                char(10),
                a!richTextItem(
                  text: "Open Items"
                )
              },
              align: "CENTER"
            )
          }
        )
      }
    ),
    a!columnLayout(
      contents: {
        a!cardLayout(
          contents: {
            a!richTextDisplayField(
              labelPosition: "COLLAPSED",
              value: {
                a!richTextItem(
                  text: tostring(local!overdueCount),
                  size: "LARGE",
                  style: "STRONG"
                ),
                char(10),
                a!richTextItem(
                  text: "Overdue Items"
                )
              },
              align: "CENTER"
            )
          }
        )
      }
    )
  }
)
```

### UX notes

- Define KPI logic clearly.
- Provide drill-through where users need to act.
- Keep card counts current but avoid expensive refresh loops.

## Recipe 13: Document upload form section

### Use when

Use this pattern for uploading documents in a process-backed form.

### Rule inputs

```text
ri!documents   Document[]   Output
```

### SAIL pattern

```appian
a!sectionLayout(
  label: "Supporting Documents",
  contents: {
    a!fileUploadField(
      label: "Upload Documents",
      value: ri!documents,
      saveInto: ri!documents,
      required: false
    )
  }
)
```

### UX notes

- Explain what file types or evidence are expected.
- Confirm document folder security in the process design.
- Store document metadata in a child document table where required.

## Recipe 14: Comments and notes section

### Use when

Use this pattern for capturing user comments, review notes or internal notes.

### SAIL pattern

```appian
a!paragraphField(
  label: "Comment",
  value: local!workingComment.commentText,
  saveInto: local!workingComment.commentText,
  required: true,
  instructions: "Enter a clear note explaining the decision or action."
)
```

### UX notes

- Use a child comment table for repeatable comments.
- Separate internal and external notes where visibility differs.
- Avoid one large parent-record notes field for a long-running case.

## Recipe 15: Approval decision form

### Use when

Use this pattern where an authorised user approves, rejects or returns a record.

### SAIL pattern

```appian
a!localVariables(
  local!workingRecord: ri!record,
  local!decision,
  local!decisionComment,

  a!formLayout(
    titleBar: a!headerTemplateSimple(
      title: "Review Decision",
      secondaryText: "Record your decision and comments"
    ),
    contents: {
      a!radioButtonField(
        label: "Decision",
        choiceLabels: { "Approve", "Return", "Reject" },
        choiceValues: { "APPROVE", "RETURN", "REJECT" },
        value: local!decision,
        saveInto: local!decision,
        required: true
      ),
      a!paragraphField(
        label: "Decision Comments",
        value: local!decisionComment,
        saveInto: local!decisionComment,
        required: or(
          local!decision = "RETURN",
          local!decision = "REJECT"
        )
      )
    },
    buttons: a!buttonLayout(
      primaryButtons: {
        a!buttonWidget(
          label: "Submit Decision",
          style: "SOLID",
          submit: true,
          saveInto: {
            a!save(local!workingRecord.decisionCode, local!decision),
            a!save(local!workingRecord.decisionComment, local!decisionComment),
            a!save(local!workingRecord.decidedBy, loggedInUser()),
            a!save(local!workingRecord.decidedOn, now()),
            a!save(ri!record, local!workingRecord)
          },
          validate: true
        )
      }
    )
  )
)
```

### UX notes

- Decision comments should be mandatory for negative or return decisions where required by policy.
- Approval action visibility must be backed by Appian security, not only UI conditions.

## Recipe 16: Mobile-safe layout pattern

### Use when

Use this pattern for pages expected to run well on desktop, tablet and mobile.

### Design pattern

```text
Header summary
Primary action
Key fields
Secondary details
Related list or compact card list
```

### SAIL pattern

```appian
a!sectionLayout(
  label: "Record Overview",
  contents: {
    a!cardLayout(
      contents: {
        a!richTextDisplayField(
          labelPosition: "COLLAPSED",
          value: {
            a!richTextItem(
              text: a!defaultValue(local!record.reference, "New Record"),
              style: "STRONG"
            ),
            char(10),
            a!richTextItem(
              text: a!defaultValue(local!record.status, "Draft")
            )
          }
        )
      }
    )
  }
)
```

### UX notes

- Avoid wide multi-column grids as the only way to complete a task.
- Use summaries and compact lists for phones.
- Test narrow screen behaviour before release.

## Recipe 17: User display pattern

### Use when

Use this pattern when displaying Appian usernames to end users.

### SAIL pattern

```appian
a!textField(
  label: "Created By",
  value: rule!APN_FMT_DisplayUser(local!record.createdBy),
  readOnly: true
)
```

### UX notes

- Do not display raw usernames where display names are expected.
- Use a standard formatting rule consistently.
- Confirm performance if displaying many users in a grid.

## Recipe 18: Null-safe date display

### Use when

Use this pattern whenever a date or datetime may be null.

### SAIL pattern

```appian
if(
  isnull(local!record.createdOn),
  "-",
  text(local!record.createdOn, "dd/MM/yyyy")
)
```

### UX notes

- Show `-` or a meaningful empty label for missing dates.
- Do not show technical null values to users.
- Use consistent date formatting for the project region.

## Recipe 19: Integration response display pattern

### Use when

Use this pattern where a SAIL screen displays data from an integration wrapper.

### SAIL pattern

```appian
a!localVariables(
  local!response: rule!APN_INT_SearchCustomer(searchText: ri!searchText),
  local!body: index(local!response, "body", {}),
  local!records: index(local!body, "records", {}),

  if(
    length(local!records) = 0,
    a!richTextDisplayField(
      labelPosition: "COLLAPSED",
      value: a!richTextItem(text: "No external records found."),
      align: "CENTER"
    ),
    a!forEach(
      items: local!records,
      expression: a!cardLayout(
        contents: {
          a!richTextDisplayField(
            labelPosition: "COLLAPSED",
            value: {
              a!richTextItem(
                text: tostring(index(fv!item, "displayName", "Unknown")),
                style: "STRONG"
              ),
              char(10),
              a!richTextItem(
                text: tostring(index(fv!item, "externalId", ""))
              )
            }
          )
        }
      )
    )
  )
)
```

### UX notes

- Do not assume integration responses are typed records.
- Use `index()` with safe defaults.
- Request a sample response payload before writing wrapper logic.

## Recipe 20: Reusable child component pattern

### Use when

Use this pattern where a block of UI is reused across forms, views or dashboards.

### Interface contract example

```text
Interface: APN_UI_RecordStatusSummary
Inputs:
  ri!statusCode   Text    Input
  ri!statusLabel  Text    Input
  ri!updatedOn    Date and Time Input
```

### SAIL pattern

```appian
a!cardLayout(
  contents: {
    a!richTextDisplayField(
      labelPosition: "COLLAPSED",
      value: {
        a!richTextItem(
          text: a!defaultValue(ri!statusLabel, "Unknown"),
          style: "STRONG"
        ),
        char(10),
        a!richTextItem(
          text: if(
            isnull(ri!updatedOn),
            "Not updated",
            "Updated " & text(ri!updatedOn, "dd/MM/yyyy")
          )
        )
      }
    )
  }
)
```

### UX notes

- Keep reusable child components small and focused.
- Do not hide database queries inside child components if it causes repeated execution.
- Prefer passing data into child components rather than re-querying repeatedly.

## UX best-practice checklist

| Check | Pass or fail |
|---|---|
| The user goal is clear on the first screen. |  |
| Required fields are visible and labelled. |  |
| Sections are grouped by business meaning. |  |
| Buttons use consistent wording. |  |
| Cancel and secondary actions are visually secondary. |  |
| Empty states explain what happened. |  |
| Validation messages explain how to fix the issue. |  |
| Status is communicated through text, not colour alone. |  |
| Record actions are visible only where relevant and authorised. |  |
| The interface is usable on desktop and mobile where required. |  |

## Performance checklist

| Check | Pass or fail |
|---|---|
| Query rules select only required fields. |  |
| Grids are paged. |  |
| Expensive integrations are not called on every refresh. |  |
| Reference data is queried once and reused. |  |
| `a!forEach()` does not contain avoidable queries. |  |
| Refresh variables are deliberate. |  |
| Large lists use search, filters or paging. |  |
| Child components do not re-query repeatedly. |  |

## Accessibility checklist

| Check | Pass or fail |
|---|---|
| Important images or icons have text equivalents where needed. |  |
| Colour is not the only way status is communicated. |  |
| Headings and sections represent real structure. |  |
| Required field labels are clear. |  |
| Validation messages are specific. |  |
| Keyboard-only use has been considered. |  |
| Screen reader behaviour has been considered for important messages. |  |

## Mobile checklist

| Check | Pass or fail |
|---|---|
| Form remains readable on narrow screens. |  |
| Buttons remain easy to use on phones. |  |
| Wide grids are avoided or redesigned for mobile. |  |
| Columns stack acceptably. |  |
| Pane layouts have a mobile alternative where needed. |  |
| Navigation is concise. |  |
| Primary action is easy to locate. |  |

## Common AI mistakes in recipes

| Mistake | Correction |
|---|---|
| Using `bold` instead of Appian-supported rich text values | Verify official value and use supported Appian style, such as `STRONG` where applicable. |
| Using `onClick` | Use Appian `saveInto`, submit buttons, dynamic links or record actions. |
| Using `<div>` or `<Button>` | Use SAIL components. |
| Using CSS classes | Use Appian component parameters only. |
| Inventing `placeholderLabel` | Verify exact component parameter. |
| Applying one component's parameter to another component | Verify parameter support per component. |
| Querying inside every grid row | Query before rendering and pass data into the grid. |
| Omitting fields from record queries | Select every field the UI reads. |
| Formatting null dates | Check `isnull()` first. |
| Using UI hiding as security | Enforce security at record, action and process layers. |

## References

Official Appian documentation:

- https://docs.appian.com/suite/help/26.4/SAIL_Recipes.html
- https://docs.appian.com/suite/help/26.4/SAIL_Components.html
- https://docs.appian.com/suite/help/26.4/interface-performance.html
- https://docs.appian.com/suite/help/26.4/refresh-behavior-interfaces.html
- https://docs.appian.com/suite/help/26.4/sail/ux-styled-icons.html
- https://docs.appian.com/suite/help/26.4/Chart_Color_Scheme.html
- https://docs.appian.com/suite/help/26.4/reference-translation-strings.html
