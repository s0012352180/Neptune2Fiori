# UI Component: sectionAttachments
**App:** ZDCI_NAD_PROJECT_REQUEST
**Type:** sap.uxap.ObjectPageSection
**Model Source:** GS_ATTACHMENT
**Title:** Attachments
**Purpose:** Displays the attachment list on the form and allows users to add or delete attachments.

---

## Control Hierarchy

```
sectionAttachments (sap.uxap.ObjectPageSection)
  └── subsectionAttachments (sap.uxap.ObjectPageSubSection)
        └── attachBlock1 (neptune.ObjectPageSubSectionContent) [position: MoreBlock]
              └── listAttachments (sap.m.List)
                    ├── tbListAttachments (sap.m.Toolbar) [position: HeaderToolbar]
                    │     ├── tbSpacerAttachments (sap.m.ToolbarSpacer)
                    │     └── btnAddAttachment (sap.m.Button)
                    └── listItemAttachment (sap.m.ObjectListItem)
```

---

## Component Attributes

### sectionAttachments — sap.uxap.ObjectPageSection
| Attribute | Value |
|-----------|-------|
| title | Attachments |
| Model Source | GS_ATTACHMENT |

### subsectionAttachments — sap.uxap.ObjectPageSubSection
| Attribute | Value |
|-----------|-------|
| mode | Expanded |

### attachBlock1 — neptune.ObjectPageSubSectionContent
| Attribute | Value |
|-----------|-------|
| position (Parent) | MoreBlock |

### listAttachments — sap.m.List
| Attribute | Value |
|-----------|-------|
| Model Source | GT_ATTACHMENTS |
| mode | Delete |
| noDataText | You have not attached any documents. |

### tbListAttachments — sap.m.Toolbar
| Attribute | Value |
|-----------|-------|
| position (Parent) | HeaderToolbar |

### tbSpacerAttachments — sap.m.ToolbarSpacer
| Attribute | Value |
|-----------|-------|
| _(no values set)_ | — |

### btnAddAttachment — sap.m.Button
| Attribute | Value |
|-----------|-------|
| icon | sap-icon:add |
| text | Add attachment |

### listItemAttachment — sap.m.ObjectListItem
| Attribute | Value |
|-----------|-------|
| icon | {GT_ATTACHMENTS-ICON} |
| intro | {GT_ATTACHMENTS-CATEGORY_TEXT} |
| title | {GT_ATTACHMENTS-FILE_NAME} |
| type | Active |

---

## Events & Code

### listAttachments — delete
```js
// Fired when user taps delete icon on a list item
var deleteIndex = Number(
    oEvent.getParameter("listItem").sId.split("-").pop()
);
modeldiaConfirmAttDelete.setProperty("/DELETE_INDEX", deleteIndex);
diaConfirmAttDelete.open();
```

### btnAddAttachment — press
```js
fu.uploadDialog.open();
```

### listItemAttachment — press
```js
displayAttachment(oEvent.oSource.getBindingContext().getObject());
```

---

## Notes
- Delete extracts the list item index from the control ID, stores it in `modeldiaConfirmAttDelete` at `/DELETE_INDEX`, then opens confirmation dialog — actual delete happens after user confirms in `diaConfirmAttDelete`
- Add button opens the Neptune file upload dialog `fu.uploadDialog` — not a standard SAP dialog
- Item press calls `displayAttachment()` helper, passing the full bound object — used to preview or download the attachment
- `attachBlock1` is in `MoreBlock` position — content is collapsed by default and expanded on demand
- List binding source: `GT_ATTACHMENTS` (returned by GETDETAIL, SAVE, GET_AUTHBY_ID AJAX calls)
