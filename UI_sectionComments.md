# UI Component: sectionComments
**App:** ZDCI_NAD_PROJECT_REQUEST
**Type:** sap.uxap.ObjectPageSection
**Purpose:** Displays previous comments on the form in a read-only text area.

---

## Control Hierarchy

```
sectionComments (sap.uxap.ObjectPageSection)
  └── subsectionComments (sap.uxap.ObjectPageSubSection)  [Model: GS_PROJ_FORM]
        └── CommentsBlock (neptune.ObjectPageSubSectionContent) [position: MoreBlock]
              └── oTextAreaPrevComments (sap.m.TextArea)
```

---

## Component Attributes

### sectionComments — sap.uxap.ObjectPageSection
| Attribute | Value |
|-----------|-------|
| title | Comments |
| titleUppercase | false |
| Model Source | _(not set)_ |

### subsectionComments — sap.uxap.ObjectPageSubSection
| Attribute | Value |
|-----------|-------|
| Model Source | GS_PROJ_FORM |
| mode | Expanded |
| title | Comments |

### CommentsBlock — neptune.ObjectPageSubSectionContent
| Attribute | Value |
|-----------|-------|
| position (Parent) | MoreBlock |

### oTextAreaPrevComments — sap.m.TextArea
| Attribute | Value |
|-----------|-------|
| value | {GS_PROJ_FORM-PREV_COMMENTS} |
| rows | 100 |
| cols | 20 |
| width | 100% |
| editable | false |
| enabled | false |
| growing | true |
| growingMaxLines | 10 |
| required | _(not set)_ |

---

## Events & Code

### oTextAreaPrevComments
| Event | Code |
|-------|------|
| change | _(not set)_ |
| liveChange | _(not set)_ |

---

## Notes
- Fully read-only section — `editable = false`, `enabled = false` on the TextArea
- Bound to `GS_PROJ_FORM-PREV_COMMENTS` — previous comments are populated by backend via GETDETAIL
- `growing = true` with `growingMaxLines = 10` — TextArea expands up to 10 lines as content grows
- `CommentsBlock` is in `MoreBlock` position — collapsed by default in the ObjectPage layout
- No events or user interaction — display only
