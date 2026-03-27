# UI Component: sectionApprovals
**App:** ZDCI_NAD_PROJECT_REQUEST
**Type:** sap.uxap.ObjectPageSection
**Model Source:** _(none set at section level)_
**Purpose:** Container section for all approval subsections — FPA, RTR, and BPC approver groups.

---

## Control Hierarchy

```
sectionApproval (sap.uxap.ObjectPageSection)
  ├── subsectionFPA  (sap.uxap.ObjectPageSubSection)
  ├── subsectionRTR  (sap.uxap.ObjectPageSubSection)
  └── subsectionBPC  (sap.uxap.ObjectPageSubSection)
```

> Each subsection (FPA / RTR / BPC) contains its own approver fields and controls.
> Document each subsection separately when PDFs are available.

---

## sectionApproval — sap.uxap.ObjectPageSection Attributes

| Attribute | Value |
|-----------|-------|
| Name | sectionApproval |
| Model Source | _(not set)_ |
| Model Path | _(not set)_ |
| title | Workflow |
| titleUppercase | false |
| showTitle | _(not set)_ |

---

## Events
| Event | Code |
|-------|------|
| ajaxError | _(not set)_ |
| ajaxSuccess | _(not set)_ |

---

## Notes
- Pure container — no model binding, no events at section level
- `titleUppercase = false` — title renders as "Workflow" in mixed case
- Three subsections: `subsectionFPA`, `subsectionRTR`, `subsectionBPC` — each maps to a separate approver group in the workflow
- Scroll targets in GETDETAIL ajaxSuccess: `subsectionFPA`, `subsectionRTR`, `subsectionBPC` — backend flags which one to scroll to via `PROJ_FLGRP_0002_MOD`, `PROJ_FLGRP_0003_MOD`, `PROJ_FLGRP_0004_MOD`
