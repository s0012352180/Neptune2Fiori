# Component Spec: `oPageDialogs` — All Application Dialogs
## Neptune → SAP Fiori Freestyle Migration
### Application: `ZDCI_NAD_PROJECT_REQUEST`

---

## 1. OVERVIEW

This file documents all dialogs in the `oPageDialogs` container. Each dialog section is self-contained with its full control hierarchy, attribute table, event handlers, and ready-to-paste Fiori XML.

**Known dialog inventory (from master tree):**

| # | Dialog ID | Purpose | Status |
|---|---|---|---|
| 1 | `diaTimeout` | Session timeout notification | ✅ Specced |
| 2 | `diaAddMultiCC` | Add additional company code | ❓ Pending |
| 3 | `diaSearchPrevProj` | Search previous project codes | ❓ Pending |
| 4 | `diaReSubmit` | Re-submit with comment | ❓ Pending |
| 5 | `diaAgents` | Show agents list | ❓ Pending |
| 6 | `diaConfirmAttDelete` | Confirm attachment delete | ❓ Pending |
| 7 | `diaAddAttachment` | Upload file attachment | ❓ Pending |
| 8 | `diaEmployeeInfo` | Employee information display | ❓ Pending |
| 9 | `diaReject` | Reject with comment | ❓ Pending |
| 10 | `diaCreate` | Create with comment | ❓ Pending |
| 11 | `diaChange` | Change with comment | ❓ Pending |
| 12 | `diaSubmit` | Submit with comment | ❓ Pending |
| 13 | `diaSubmitWthErr` | Submit with error comment | ❓ Pending |
| 14 | `diaClose` | Close/cancel with comment | ❓ Pending |
| 15 | `diaMessage` | Generic message display | ❓ Pending |
| 16 | `diaSearchAuthBy` | Search authorized-by person | ❓ Pending |
| 17 | `diaSearchOnBehalf` | Search on-behalf-of person | ❓ Pending |
| 18 | `diaSearchCostCenter` | Search cost center | ❓ Pending |
| 19 | `diaGeography` | Geography lookup | ❓ Pending |
| 20 | `diaBrand` | Brand lookup | ❓ Pending |
| 21 | `diaLocation` | Location lookup | ❓ Pending |
| 22 | `diaDelDraft` | Delete draft confirmation | ❓ Pending |

---

---

# DIALOG 1: `diaTimeout`

---

## D1.1 OVERVIEW

| Property | Value |
|---|---|
| **Dialog ID** | `diaTimeout` |
| **Type** | `sap.m.Dialog` |
| **Title** | `Session Timeout` |
| **contentWidth** | `300px` |
| **Purpose** | Shown when the user's session times out. Contains a message and a Close button that clears busy state. |

---

## D1.2 CONTROL HIERARCHY

```
diaTimeout  [sap.m.Dialog]
  ├── Associations:
  │     └── rightButton → butTimeoutClose
  ├── content:
  │     └── oTextAreaTimeout  [sap.m.TextArea]
  └── (buttons via association) → butTimeoutClose  [sap.m.Button]
```

---

## D1.3 DIALOG ATTRIBUTES

| Attribute | Value |
|---|---|
| name | `diaTimeout` |
| title | `Session Timeout` |
| contentWidth | `300px` |
| rightButton (association) | `butTimeoutClose` |

---

## D1.4 CONTENT CONTROLS

### D1.4.1 `oTextAreaTimeout` — TextArea

| Attribute | Value |
|---|---|
| name | `oTextAreaTimeout` |
| rows | `4` |
| editable | `false` |
| maxLength | `0` *(unlimited)* |
| value | `Your session has Timed Out. Click Close to return to the Fiori Launchpad.` |

### D1.4.2 `butTimeoutClose` — Button

| Attribute | Value |
|---|---|
| name | `butTimeoutClose` |
| text | `Close` |

**Events:**

| Event | Code |
|---|---|
| `press` | `oApp.setBusy(false); diaTimeout.close();` |

**Fiori Controller equivalent:**
```javascript
onTimeoutClose: function () {
    this.getView().setBusy(false);
    this.byId("diaTimeout").close();
},
```

---

## D1.5 FIORI XML

```xml
<m:Dialog
    id="diaTimeout"
    title="Session Timeout"
    contentWidth="300px">
  <m:content>
    <m:TextArea
        id="oTextAreaTimeout"
        rows="4"
        editable="false"
        value="Your session has Timed Out. Click Close to return to the Fiori Launchpad." />
  </m:content>
  <m:endButton>
    <m:Button
        id="butTimeoutClose"
        text="Close"
        press=".onTimeoutClose" />
  </m:endButton>
</m:Dialog>
```

---

## D1.6 NOTES

| # | Item | Status |
|---|---|---|
| 1 | `diaTimeout` is opened by the Neptune `discoveryFiori` session timeout handler — in Fiori, replicate by attaching a session timer that calls `this.byId("diaTimeout").open()` on timeout | ❓ Confirm trigger mechanism |
| 2 | `rightButton` association in Neptune maps to `endButton` aggregation in Fiori `sap.m.Dialog` | ✅ Mapped |
| 3 | Message text references `"Fiori Launchpad"` — confirm whether this is the correct target URL or if it should navigate to a specific page | ❓ Open |

---

---

# DIALOGS 2–22: Pending

*(Each will be added as a new numbered section when the corresponding PDF is uploaded.)*

---

## APPENDIX A — Dialog Pattern Reference

All comment/text-area dialogs in this app (diaCreate, diaChange, diaSubmit, diaReject, diaClose, diaReSubmit, diaSubmitWthErr) follow the same structural pattern:

```
diaXxx  [sap.m.Dialog]
  ├── content:
  │     └── oTextAreaXxx  [sap.m.TextArea]  ← user enters comment
  ├── beginButton → butXxxSave  [sap.m.Button]  ← confirm action
  └── endButton   → butXxxCancel  [sap.m.Button]  ← cancel
```

**Common attributes across comment dialogs:**
- `contentWidth`: typically `400px`–`600px`
- `oTextAreaXxx`: `rows=5`, `editable=true`, bound to a temp model or read directly on press
- Save button triggers the corresponding AJAX call (SAVE, CREATE, SUBMIT, REJECT, etc.)
- Cancel button just calls `diaXxx.close()`

All person-search dialogs (diaSearchOnBehalf, diaSearchAuthBy, diaSearchCostCenter) follow this pattern:

```
diaXxx  [sap.m.Dialog or sap.m.SelectDialog]
  └── listXxx_Person / listXxx  [sap.m.List or sap.m.SelectDialog]
        └── listItemXxx  [sap.m.StandardListItem]
```

**Common behavior:**
- `itemPress` or `selectionChange` sets the value back to the source input field via `this._oUserField.setValue(...)`
- Dialog closes after selection

---

## APPENDIX B — Dialog Trigger Map

| Dialog | Opened By | Closed By |
|---|---|---|
| `diaTimeout` | Session timeout handler | `butTimeoutClose` press |
| `diaAddMultiCC` | `btnAddCompCode` press | `btnAddMultiCCOK` / `btnAddMultiCCCancel` |
| `diaSearchPrevProj` | `oSelectPrevPCode` value help | Item selection |
| `diaReSubmit` | `butResubmit` press | `butReSubmitSave` / `butReSubmitCancel` |
| `diaAgents` | `btnAgentsClose` context | `btnAgentsClose` press |
| `diaConfirmAttDelete` | Delete attachment action | `btnAttDeleteYes` / `btnAttDeleteNo` |
| `diaAddAttachment` | All upload buttons + `btnAddAttachment` | `diaAddAttachmentClose` / file selected |
| `diaEmployeeInfo` | Requester name click | Close button |
| `diaReject` | `butReject` press | `butRejectOK` / `butRejectCancel` |
| `diaCreate` | `butCreate` press | `butCreateSave` / `butCreateCancel` |
| `diaChange` | `butChange` press | `butChgSave` / `butChgCancel` |
| `diaSubmit` | `butSubmit` press | `butSubmitSave` / `butSubmitCancel` |
| `diaSubmitWthErr` | `butSubmitWthErr` press | `butSubWthErrSave` / `butSubWthErrCancel` |
| `diaClose` | `butClose` press | `butCloseSubmit` / `butCloseCancel` |
| `diaMessage` | `discoveryFiori.showMessage()` | `btnMessageClose` |
| `diaSearchAuthBy` | `inpPersonResponsible` valueHelpRequest | Person selected |
| `diaSearchOnBehalf` | `inpOnBehalfOf` / `inpPersonResponsible` valueHelpRequest | Person selected |
| `diaSearchCostCenter` | `inpCostCenter` valueHelpRequest | Cost center selected |
| `diaGeography` | `inpGeography` valueHelpRequest | Geography selected |
| `diaBrand` | `inpBrand` valueHelpRequest | Brand selected |
| `diaLocation` | `inpLocation` valueHelpRequest | Location selected |
| `diaDelDraft` | `butDDraft` press | `butDDraftCancel` / `butDDraft` confirm |
