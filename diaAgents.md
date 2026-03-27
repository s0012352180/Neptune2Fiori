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

---

# DIALOG 2: `diaAddMultiCC`

---

## D2.1 OVERVIEW

| Property | Value |
|---|---|
| **Dialog ID** | `diaAddMultiCC` |
| **Type** | `sap.m.Dialog` |
| **Title** | `Add Additional Company Code` |
| **contentWidth** | `600px` |
| **Model Source** | `GS_MULTIPLECCODE` |
| **Purpose** | Allows user to select an additional company code and its associated cost center, then adds a new row to `tabMultiCC`. |
| **Opened by** | `btnAddCompCode` press |
| **beginButton** | `btnAddMultiCCOK` (OK) |
| **endButton / cancel** | `btnAddMultiCCCancel` |

---

## D2.2 CONTROL HIERARCHY

```
diaAddMultiCC  [sap.m.Dialog]
  ├── Associations:
  │     ├── beginButton → btnAddMultiCCOK
  │     └── endButton   → btnAddMultiCCCancel  (closes dialog)
  └── content:
        └── sfAddMultiCCodes  [sap.ui.layout.form.SimpleForm]
              ├── lblMultiCCode          [sap.m.Label]
              ├── oSelectMultiCCode      [sap.m.Select]
              ├── lblMultiCostCenter     [sap.m.Label]
              └── inpMultiCostCenter     [sap.m.Input]
```

---

## D2.3 DIALOG ATTRIBUTES

| Attribute | Value |
|---|---|
| name | `diaAddMultiCC` |
| title | `Add Additional Company Code` |
| contentWidth | `600px` |
| Model Source | `GS_MULTIPLECCODE` |
| beginButton (association) | `btnAddMultiCCOK` |
| endButton / cancel | `btnAddMultiCCCancel` |

---

## D2.4 SIMPLEFORM — `sfAddMultiCCodes`

| Attribute | Value |
|---|---|
| name | `sfAddMultiCCodes` |
| layout | `ResponsiveGridLayout` |
| editable | `true` |
| labelSpanL | `12` |
| labelSpanM | `12` |

---

## D2.5 CONTENT CONTROLS

### D2.5.1 `lblMultiCCode` — Label

| Attribute | Value |
|---|---|
| name | `lblMultiCCode` |
| description | `Multiple Company Code` |
| text | `Additional Company Code` |

### D2.5.2 `oSelectMultiCCode` — Select

| Attribute | Value |
|---|---|
| name | `oSelectMultiCCode` |
| description | `Select Multiple Company Code` |
| enabled | `{GS_MULTIPLECCODE-PROJ_FLGRP_0001}` |
| selectedKey | `{GS_MULTIPLECCODE-COMP_CODE}` |
| setEnableCache | `true` |
| setInitLoad | `cache` |
| columns | `1` |

**Model Source (Application Class):**

| Property | Value |
|---|---|
| Model Source | `GT_ADD_COMP_CCODE` |
| Value Field | `VALUE` |

**Events:**

| Event | Code |
|---|---|
| `change` | See §D2.7.1 |

---

### D2.5.3 `lblMultiCostCenter` — Label

| Attribute | Value |
|---|---|
| name | `lblMultiCostCenter` |
| description | `Multiple Cost Center` |
| text | `Cost Center` |

### D2.5.4 `inpMultiCostCenter` — Input

| Attribute | Value |
|---|---|
| name | `inpMultiCostCenter` |
| description | `Multiple Cost Center` |
| enabled | `{GS_MULTIPLECCODE-PROJ_FLGRP_0001}` |
| showSuggestion | `true` |
| showValueHelp | `true` |

**Events:**

| Event | Code |
|---|---|
| `valueHelpRequest` | See §D2.7.2 |

---

## D2.6 BUTTON CONTROLS

### D2.6.1 `btnAddMultiCCOK` — Button (OK / Add row)

| Attribute | Value |
|---|---|
| name | `btnAddMultiCCOK` |
| text | `OK` |

**Events — press (full code from PDF pages 2–4):**

```javascript
// Handler name: onAddMultiCCOK
;   // (debug breakpoint in Neptune source)

var newCompcodes   = oSelectMultiCCode.getSelectedKey();
var newCostCenter  = inpMultiCostCenter._getInputValue();

// Determine region label from PROCESSING_REGION
var region;
if      (modeloPageProj.oData.PROCESSING_REGION === "U") { region = "US/LA"; }
else if (modeloPageProj.oData.PROCESSING_REGION === "E") { region = "Europe"; }
else if (modeloPageProj.oData.PROCESSING_REGION === "A") { region = "Asia"; }

// Validation
if (newCompcodes.length === 0) {
    discoveryFiori.showMessage(
        "Select additional company code",
        sap.ui.core.ValueState.Error);
    oSelectMultiCCode.focus();

} else if (newCompcodes.length > 0 && newCostCenter.length === 0) {
    discoveryFiori.showMessage(
        "Select Cost Center before adding new company code",
        sap.ui.core.ValueState.Error);
    inpMultiCostCenter.focus();

} else {
    // Add new row to table model
    modeltabMultiCC.oData.push({
        STATUS:    'sap-icon://add-document',
        REGION:    region,
        COMP_CODE: newCompcodes,
        COMP_TEXT: oSelectMultiCCode._getSelectedItemText().substring(5,
                       (oSelectMultiCCode._getSelectedItemText().length - 5)),
        KOSTL_CODE: newCostCenter.substring(0, 10),
        KOSTL_TEXT: newCostCenter.substring(11, (newCostCenter.length - 10)),
    });
    modeltabMultiCC.updateBindings();
    modeltabMultiCC.refresh();
    diaAddMultiCC.close();
    oApp.setBusy(false);
}
```

> ⚠️ **Critical notes:**
> - `inpMultiCostCenter._getInputValue()` is a **private API** call (underscore prefix). In Fiori, replace with `inpMultiCostCenter.getValue()`.
> - `oSelectMultiCCode._getSelectedItemText()` is also a private API. Replace with `oSelectMultiCCode.getSelectedItem().getText()`.
> - The `COMP_TEXT` extraction uses `substring(5, length-5)` to strip leading/trailing characters — this appears to strip a key prefix from the display text. Verify the actual data format from `GT_ADD_COMP_CCODE` and adjust accordingly.
> - `KOSTL_CODE` takes first 10 chars; `KOSTL_TEXT` takes chars from position 11 onward minus 10. This string-splitting assumes the input value is formatted as `"CODE TEXT_PADDING"`. Verify format returned by cost center search.

**Fiori Controller equivalent:**
```javascript
onAddMultiCCOK: function () {
    var oPageModel  = this.getView().getModel("pageProj");
    var oMultiModel = this.getView().getModel("additionalCCode");
    var oSelect     = this.byId("oSelectMultiCCode");
    var oInput      = this.byId("inpMultiCostCenter");

    var sCompCode    = oSelect.getSelectedKey();
    var sCostCenter  = oInput.getValue();
    var sRegion      = { "U": "US/LA", "E": "Europe", "A": "Asia" }
                         [oPageModel.getProperty("/PROCESSING_REGION")] || "";

    if (!sCompCode) {
        sap.m.MessageBox.error("Select additional company code");
        oSelect.focus();
        return;
    }
    if (!sCostCenter) {
        sap.m.MessageBox.error("Select Cost Center before adding new company code");
        oInput.focus();
        return;
    }

    var aData = oMultiModel.getData() || [];
    var sCompText = oSelect.getSelectedItem() ? oSelect.getSelectedItem().getText() : "";
    // Strip prefix/suffix as per Neptune source (5 chars each side)
    sCompText = sCompText.substring(5, sCompText.length - 5);

    aData.push({
        STATUS:     "sap-icon://add-document",
        REGION:     sRegion,
        COMP_CODE:  sCompCode,
        COMP_TEXT:  sCompText,
        KOSTL_CODE: sCostCenter.substring(0, 10),
        KOSTL_TEXT: sCostCenter.substring(11, sCostCenter.length - 10),
        ISULTRUE:   true
    });
    oMultiModel.setData(aData);
    this.byId("diaAddMultiCC").close();
    this.getView().setBusy(false);
},
```

### D2.6.2 `btnAddMultiCCCancel` — Button (Cancel)

| Attribute | Value |
|---|---|
| name | `btnAddMultiCCCancel` |

**Events:**

| Event | Code |
|---|---|
| `press` | `diaAddMultiCC.close();` |

**Fiori Controller equivalent:**
```javascript
onAddMultiCCCancel: function () {
    this.byId("diaAddMultiCC").close();
},
```

---

## D2.7 EVENT HANDLERS

### D2.7.1 `oSelectMultiCCode` — change event

```javascript
// Handler name: onMultiCCodeChange
var companycode = oSelectMultiCCode.getSelectedKey();
if (companycode) {
    modelUrlParameters.setProperty("/COMPANY_CODE", companycode);
    getOnlineGetCostCenter('*');
    // Clear Cost Center if already selected
    inpMultiCostCenter.setValue("");
    inpMultiCostCenter.fireChange();
}
```

> **Same pattern as `oSelectCompCode.change`** in `sfBudgetProj2` — triggers cost center list reload and clears the input.

**Fiori Controller equivalent:**
```javascript
onMultiCCodeChange: function () {
    var sCompCode = this.byId("oSelectMultiCCode").getSelectedKey();
    if (sCompCode) {
        this.getView().getModel("urlParameters").setProperty("/COMPANY_CODE", sCompCode);
        this._getOnlineGetCostCenter("*");
        this.byId("inpMultiCostCenter").setValue("");
        this.byId("inpMultiCostCenter").fireChange();
    }
},
```

---

### D2.7.2 `inpMultiCostCenter` — valueHelpRequest event

```javascript
//////////// ;
var companycode = oSelectMultiCCode.getSelectedKey();
if (companycode) {
    modelUrlParameters.setProperty("/COMPANY_CODE", companycode);
    diaSearchCostCenter.open();
} else {
    discoveryFiori.showMessage(
        "Company code is required to select Cost Center",
        sap.ui.core.ValueState.Error);
}
```

> **Same guard pattern as `inpCostCenter.valueHelpRequest`** in `sfBudgetProj2`.

**Fiori Controller equivalent:**
```javascript
onMultiCostCenterValueHelp: function () {
    var sCompCode = this.byId("oSelectMultiCCode").getSelectedKey();
    if (sCompCode) {
        this.getView().getModel("urlParameters").setProperty("/COMPANY_CODE", sCompCode);
        this.byId("diaSearchCostCenter").open();
    } else {
        sap.m.MessageBox.error("Company code is required to select Cost Center");
    }
},
```

---

## D2.8 FIORI XML

```xml
<m:Dialog
    id="diaAddMultiCC"
    title="Add Additional Company Code"
    contentWidth="600px"
    beforeClose=".onAddMultiCCCancel">

  <m:content>
    <form:SimpleForm
        id="sfAddMultiCCodes"
        editable="true"
        layout="ResponsiveGridLayout"
        labelSpanL="12"
        labelSpanM="12">
      <form:content>

        <!-- Additional Company Code -->
        <m:Label id="lblMultiCCode" text="Additional Company Code" />
        <m:Select id="oSelectMultiCCode"
            enabled="{multiCC>PROJ_FLGRP_0001}"
            selectedKey="{multiCC>COMP_CODE}"
            change=".onMultiCCodeChange">
          <!-- Items bound to GT_ADD_COMP_CCODE model -->
        </m:Select>

        <!-- Cost Center -->
        <m:Label id="lblMultiCostCenter" text="Cost Center" />
        <m:Input id="inpMultiCostCenter"
            enabled="{multiCC>PROJ_FLGRP_0001}"
            showSuggestion="true"
            showValueHelp="true"
            valueHelpRequest=".onMultiCostCenterValueHelp" />

      </form:content>
    </form:SimpleForm>
  </m:content>

  <m:beginButton>
    <m:Button id="btnAddMultiCCOK"
        text="OK"
        press=".onAddMultiCCOK" />
  </m:beginButton>
  <m:endButton>
    <m:Button id="btnAddMultiCCCancel"
        text="Cancel"
        press=".onAddMultiCCCancel" />
  </m:endButton>

</m:Dialog>
```

---

## D2.9 NOTES

| # | Item | Status |
|---|---|---|
| 1 | `inpMultiCostCenter._getInputValue()` is a private API — replace with `.getValue()` in Fiori | ✅ Fixed in spec |
| 2 | `oSelectMultiCCode._getSelectedItemText()` is a private API — replace with `.getSelectedItem().getText()` | ✅ Fixed in spec |
| 3 | `COMP_TEXT` is extracted with `substring(5, length-5)` — verify actual data format from `GT_ADD_COMP_CCODE` and adjust substring offsets if needed | ⚠️ Verify |
| 4 | `KOSTL_CODE`/`KOSTL_TEXT` split at fixed char positions (0–10 / 11–end-10) — verify cost center value format from `diaSearchCostCenter` selection return | ⚠️ Verify |
| 5 | `oSelectMultiCCode.change` and `inpMultiCostCenter.valueHelpRequest` both share the same `COMPANY_CODE` + `getOnlineGetCostCenter` pattern as `oSelectCompCode` in `sfBudgetProj2` — the same `_getOnlineGetCostCenter` private method services both | ✅ Confirmed |
| 6 | `diaSearchCostCenter` is shared between this dialog's cost center lookup and `sfBudgetProj2`'s cost center lookup — the same dialog, same `COMPANY_CODE` gate | ✅ Confirmed |
| 7 | On successful row add, `oApp.setBusy(false)` is called — this should be `this.getView().setBusy(false)` in Fiori | ✅ Fixed in spec |

---

---

# DIALOG 3: `diaReSubmit`

---

## D3.1 OVERVIEW

| Property | Value |
|---|---|
| **Dialog ID** | `diaReSubmit` |
| **Type** | `sap.m.Dialog` |
| **Title** | `Resubmit Confirmation` |
| **contentHeight** | `200px` |
| **contentWidth** | `500px` |
| **draggable** | `true` |
| **verticalScrolling** | `false` |
| **Model Source** | `WA_COMMENTS` |
| **Purpose** | Allows user to enter a comment before resubmitting a rejected form. Sets ITL flag and calls `getOnlineResubmit()`. |
| **Opened by** | `butResubmit` press |
| **leftButton** | `butReSubmitCancel` |
| **rightButton** | `butReSubmitSave` |

---

## D3.2 CONTROL HIERARCHY

```
diaReSubmit  [sap.m.Dialog]
  ├── Associations:
  │     ├── leftButton  → butReSubmitCancel
  │     └── rightButton → butReSubmitSave
  └── content:
        └── oTextAreaReSubmit  [sap.m.TextArea]
```

---

## D3.3 DIALOG ATTRIBUTES

| Attribute | Value |
|---|---|
| name | `diaReSubmit` |
| title | `Resubmit Confirmation` |
| contentHeight | `200px` |
| contentWidth | `500px` |
| draggable | `true` |
| verticalScrolling | `false` |
| Model Source | `WA_COMMENTS` |
| leftButton | `butReSubmitCancel` |
| rightButton | `butReSubmitSave` |

**Events:**

| Event | Code |
|---|---|
| `beforeOpen` | `oTextAreaReSubmit.setValue();` — clears old comment |

---

## D3.4 CONTENT CONTROLS

### D3.4.1 `oTextAreaReSubmit` — TextArea

| Attribute | Value |
|---|---|
| name | `oTextAreaReSubmit` |
| rows | `4` |
| editable | `true` |
| placeholder | `Add Comments` |
| value | `{WA_COMMENTS-COMMENTS}` |

---

## D3.5 BUTTON CONTROLS

### D3.5.1 `butReSubmitSave` — Button (Submit)

| Attribute | Value |
|---|---|
| name | `butReSubmitSave` |
| text | `Submit` |
| type | `Accept` |

**Events — press (full code from PDF pages 3–4):**

```javascript
//////////// ;
diaReSubmit.close();
oApp.setBusy(true);

modeloPageProj.oData.COMMENTS = oTextAreaReSubmit.getValue();
modeloPageProj.refresh();

if (rbITLYes.getSelected()) {
    modeloPageProj.oData.ITL = "Y";
    modeloPageProj.refresh();
} else {
    modeloPageProj.oData.ITL = "N";
    modeloPageProj.refresh();
}

clear_value();          // Clear Value based on condition
getOnlineResubmit();
diaReSubmit.close();    // NOTE: called twice — once at top, once here
oApp.setBusy(false);
```

> ⚠️ `diaReSubmit.close()` appears twice — once at start, once at end. Likely a copy-paste artifact. In Fiori call only once.

**Fiori Controller equivalent:**
```javascript
onReSubmitSave: function () {
    var oModel   = this.getView().getModel("pageProj");
    var sComment = this.byId("oTextAreaReSubmit").getValue();
    oModel.setProperty("/COMMENTS", sComment);
    oModel.setProperty("/ITL", this.byId("rbITLYes").getSelected() ? "Y" : "N");
    this._clearValue();
    this.getView().setBusy(true);
    this.byId("diaReSubmit").close();
    this._getOnlineResubmit();
},
```

---

### D3.5.2 `butReSubmitCancel` — Button (Cancel)

| Attribute | Value |
|---|---|
| name | `butReSubmitCancel` |
| text | `Cancel` |
| type | `Transparent` |
| icon | `Deny` — likely `sap-icon://decline` |

**Events:**

| Event | Code |
|---|---|
| `press` | `diaReSubmit.close(); oApp.setBusy(false);` |

**Fiori Controller equivalent:**
```javascript
onReSubmitCancel: function () {
    this.byId("diaReSubmit").close();
    this.getView().setBusy(false);
},
```

---

## D3.6 `beforeOpen` EVENT

```javascript
oTextAreaReSubmit.setValue();  // equivalent to setValue("")
```

**Fiori Controller equivalent:**
```javascript
onReSubmitBeforeOpen: function () {
    this.byId("oTextAreaReSubmit").setValue("");
},
```

---

## D3.7 FIORI XML

```xml
<m:Dialog
    id="diaReSubmit"
    title="Resubmit Confirmation"
    contentHeight="200px"
    contentWidth="500px"
    draggable="true"
    verticalScrolling="false"
    beforeOpen=".onReSubmitBeforeOpen">
  <m:content>
    <m:TextArea
        id="oTextAreaReSubmit"
        rows="4"
        editable="true"
        placeholder="Add Comments"
        value="{waComments>COMMENTS}" />
  </m:content>
  <m:beginButton>
    <m:Button id="butReSubmitSave" text="Submit" type="Accept"
        press=".onReSubmitSave" />
  </m:beginButton>
  <m:endButton>
    <m:Button id="butReSubmitCancel" text="Cancel" type="Transparent"
        press=".onReSubmitCancel" />
  </m:endButton>
</m:Dialog>
```

> Neptune `leftButton`/`rightButton` associations → Fiori `beginButton`/`endButton` aggregations.

---

## D3.8 NOTES

| # | Item | Status |
|---|---|---|
| 1 | `diaReSubmit.close()` called twice in Neptune — port only once | ✅ Fixed |
| 2 | `setValue()` with no arg = `setValue("")` — explicit in Fiori | ✅ Fixed |
| 3 | `WA_COMMENTS` model — in Fiori use a simple JSON model `waComments` with `COMMENTS` property, reset on `beforeOpen` | ✅ Plan |
| 4 | ITL flag reads `rbITLYes` from `sfBudgetProj` — cross-view reference; ensure `byId` resolves correctly | ⚠️ Verify |
| 5 | `clear_value()` is a stub — port as `this._clearValue()` | ✅ Confirmed |
| 6 | `getOnlineResubmit()` — AJAX object `RESUBMIT`; add to AJAX spec | ❓ Pending |
| 7 | `butReSubmitCancel` icon `Deny` — use `sap-icon://decline` or omit icon for text-only Cancel | ⚠️ Verify |

---

---

# DIALOG 4: `diaAgents`

---

## D4.1 OVERVIEW

| Property | Value |
|---|---|
| **Dialog ID** | `diaAgents` |
| **Type** | `sap.m.Dialog` |
| **Title** | *(blank — no title set)* |
| **Model Source** | *(blank on dialog itself)* |
| **Purpose** | Displays a list of agents (ZCA agent list) for informational purposes. Read-only list with a Close button. |
| **Opened by** | `btnAgentsClose` context / agent-related action |
| **endButton** | `btnAgentsClose` |

---

## D4.2 CONTROL HIERARCHY

```
diaAgents  [sap.m.Dialog]
  ├── Associations:
  │     └── endButton → btnAgentsClose
  └── content:
        └── listAgents  [sap.m.List]
              └── listItemAgent  [sap.m.StandardListItem]
```

---

## D4.3 DIALOG ATTRIBUTES

| Attribute | Value |
|---|---|
| name | `diaAgents` |
| title | *(blank)* |
| endButton | `btnAgentsClose` |

---

## D4.4 CONTENT CONTROLS

### D4.4.1 `listAgents` — List

| Attribute | Value |
|---|---|
| name | `listAgents` |
| Model Source | `GT_ZCA_AGENT_LIST` |
| XSLT Transformation | *(checked in Neptune — indicates backend XSLT transforms data)* |

**Events:**

| Event | Code |
|---|---|
| `itemPress` | *(blank — list is display-only)* |
| `growingFinished` | *(blank)* |
| `beforeOpenContextMenu` | *(blank)* |
| `delete` | *(blank)* |

### D4.4.2 `listItemAgent` — StandardListItem

| Attribute | Value |
|---|---|
| name | `listItemAgent` |
| title | `{GT_ZCA_AGENT_LIST-AGENT_NAME}` |
| description | *(blank)* |
| icon | *(blank)* |
| info | *(blank)* |

> The list item template binds `title` to `GT_ZCA_AGENT_LIST-AGENT_NAME`. In Fiori, bind items to the `agentList` named model with relative path `{AGENT_NAME}`.

---

## D4.5 BUTTON CONTROLS

### D4.5.1 `btnAgentsClose` — Button (Close)

| Attribute | Value |
|---|---|
| name | `btnAgentsClose` |
| icon | `sap-icon://sys-cancel` |

**Events:**

| Event | Code |
|---|---|
| `press` | `diaAgents.close();` |

**Fiori Controller equivalent:**
```javascript
onAgentsClose: function () {
    this.byId("diaAgents").close();
},
```

---

## D4.6 FIORI XML

```xml
<m:Dialog
    id="diaAgents">
  <m:content>
    <m:List
        id="listAgents"
        items="{agentList>/}">
      <m:items>
        <m:StandardListItem
            id="listItemAgent"
            title="{AGENT_NAME}" />
      </m:items>
    </m:List>
  </m:content>
  <m:endButton>
    <m:Button
        id="btnAgentsClose"
        icon="sap-icon://sys-cancel"
        press=".onAgentsClose" />
  </m:endButton>
</m:Dialog>
```

---

## D4.7 NOTES

| # | Item | Status |
|---|---|---|
| 1 | `listAgents` Model Source is `GT_ZCA_AGENT_LIST` with XSLT Transformation checked — in Fiori load data from backend into a named JSON model `agentList` (XSLT transformation is a Neptune-specific mechanism; Fiori receives JSON directly) | ⚠️ Verify backend response format |
| 2 | `listItemAgent` title binds to `GT_ZCA_AGENT_LIST-AGENT_NAME` — confirm exact field name from backend response | ❓ Open |
| 3 | No `itemPress` handler — list is display-only | ✅ Confirmed |
| 4 | `diaAgents` has no title — confirm whether a title should be added in Fiori | ❓ Open |
| 5 | Dialog is triggered from `btnAgentsClose` context in master tree — confirm exact trigger condition (likely from a workflow step showing assigned agents) | ❓ Open |

---

---

# DIALOGS 5–22: Pending

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
