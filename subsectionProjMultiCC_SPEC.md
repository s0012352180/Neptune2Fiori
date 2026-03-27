# Component Spec: `subsectionProjMultiCC`
## Neptune → SAP Fiori Freestyle Migration
### Application: `ZDCI_NAD_PROJECT_REQUEST`

---

## 1. COMPONENT OVERVIEW

| Property | Value |
|---|---|
| **Neptune SubSection ID** | `subsectionProjMultiCC` |
| **Neptune Type** | `sap.uxap.ObjectPageSubSection` |
| **Title** | `Additional Company Code` |
| **Description** | `Section to select multiple CCode` |
| **Mode** | `{DS_PROJ_FORM-PROJ_FLGRP_0001_MOD}` |
| **Visible** | `false` *(hardcoded — shown based on runtime conditions)* |
| **Block ID** | `ProjMultiCCode` |
| **Block Position** | `MoreBlock` |
| **Parent Section** | `sectionProj` |

---

## 2. COMPLETE CONTROL HIERARCHY

```
subsectionProjMultiCC  [sap.uxap.ObjectPageSubSection]
  └── ProjMultiCCode  [neptune.ObjectPageSubSectionContent / MoreBlock]
        └── sfBudgetCCode  [sap.ui.layout.form.SimpleForm]
              └── tabMultiCC  [sap.m.Table]
                    ├── tbTabMultiCCData  [sap.m.Toolbar]  ← HeaderToolbar
                    │     ├── tbSpacerCCode       [sap.m.ToolbarSpacer]
                    │     └── btnAddCompCode      [sap.m.Button]
                    ├── colStatusICON            [sap.m.Column]
                    ├── colCRegion               [sap.m.Column]
                    ├── colCCompCode             [sap.m.Column]
                    ├── colCCompCode_Text         [sap.m.Column]
                    ├── colCostCenterCode         [sap.m.Column]
                    ├── colCostCenterText         [sap.m.Column]
                    ├── colCDelete               [sap.m.Column]
                    └── listItemMultiCCode  [sap.m.ColumnListItem]
                          ├── oFormStatusIcon     [sap.ui.core.Icon]
                          ├── txtCMultiCRegion    [sap.m.Text]
                          ├── txtCMultiCCode      [sap.m.Text]
                          ├── txtCMultiCCode_Text [sap.m.Text]
                          ├── txtCMultiCostCenter [sap.m.Text]
                          ├── txtCMultiCCenter_Text [sap.m.Text]
                          └── btnDeleteCCode      [sap.m.Button]
```

---

## 3. SUBSECTION & BLOCK ATTRIBUTES

### 3.1 `subsectionProjMultiCC` — ObjectPageSubSection

| Attribute | Value |
|---|---|
| name | `subsectionProjMultiCC` |
| description | `Section to select multiple CCode` |
| title | `Additional Company Code` |
| mode | `{DS_PROJ_FORM-PROJ_FLGRP_0001_MOD}` |
| visible | `false` *(initial; shown at runtime)* |

### 3.2 `ProjMultiCCode` — Block

| Attribute | Value |
|---|---|
| name | `ProjMultiCCode` |
| description | `Multiple Company Code` |
| position | `MoreBlock` |

### 3.3 `sfBudgetCCode` — SimpleForm

| Attribute | Value |
|---|---|
| name | `sfBudgetCCode` |
| description | `Multiple Company Code` |
| editable | `false` *(table inside; no direct form editing)* |

---

## 4. TABLE SPECIFICATION — `tabMultiCC`

### 4.1 `tabMultiCC` — Table

| Attribute | Value |
|---|---|
| name | `tabMultiCC` |
| description | `Table for multiple CC` |
| mode | `None` |
| noDataText | `No additional company code` |
| showSeparators | `Inner` |
| styleClass | `busy-border` |
| growingThreshold | `5` |

---

## 5. TOOLBAR — `tbTabMultiCCData`

### 5.1 `tbTabMultiCCData` — Toolbar (HeaderToolbar of `tabMultiCC`)

| Attribute | Value |
|---|---|
| name | `tbTabMultiCCData` |
| position | `HeaderToolbar` |

**Children:**

#### 5.1.1 `tbSpacerCCode` — ToolbarSpacer

| Attribute | Value |
|---|---|
| name | `tbSpacerCCode` |

#### 5.1.2 `btnAddCompCode` — Button

| Attribute | Value |
|---|---|
| name | `btnAddCompCode` |
| text | `Additional Company Code` |
| icon | `#` *(icon value not fully readable in PDF — likely `sap-icon://add`)* |
| enabled | `{DT_ADDITIONAL_CCODE-PROJ_FLGRP_0001}` |

**Events:**

| Event | Code |
|---|---|
| `press` | Anonymous Function — See §7.1 |

---

## 6. COLUMN SPECIFICATIONS — `tabMultiCC`

### 6.1 `colStatusICON` — Column

| Attribute | Value |
|---|---|
| name | `colStatusICON` |
| header text | `Status` |

### 6.2 `colCRegion` — Column

| Attribute | Value |
|---|---|
| name | `colCRegion` |
| header text | `Region` |

### 6.3 `colCCompCode` — Column

| Attribute | Value |
|---|---|
| name | `colCCompCode` |
| header text | `Company Code` |

### 6.4 `colCCompCode_Text` — Column

| Attribute | Value |
|---|---|
| name | `colCCompCode_Text` |
| header text | `Company Description` |

### 6.5 `colCostCenterCode` — Column

| Attribute | Value |
|---|---|
| name | `colCostCenterCode` |
| header text | `Cost Center Code` |

### 6.6 `colCostCenterText` — Column

| Attribute | Value |
|---|---|
| name | `colCostCenterText` |
| header text | `Cost Center Text` |

### 6.7 `colCDelete` — Column

| Attribute | Value |
|---|---|
| name | `colCDelete` |
| header text | `Delete` |

---

## 7. LIST ITEM TEMPLATE — `listItemMultiCCode`

### 7.1 `listItemMultiCCode` — ColumnListItem

| Attribute | Value |
|---|---|
| name | `listItemMultiCCode` |
| description | `List Item for multiple CCode` |
| type | `Active` |

**Child cells (in order):**

#### 7.1.1 `oFormStatusIcon` — Icon

| Attribute | Value |
|---|---|
| name | `oFormStatusIcon` |
| src | `{DT_ADDITIONAL_CCODE-STATUS}` |
| color | `Green` *(default; may be dynamic)* |

#### 7.1.2 `txtCMultiCRegion` — Text

| Attribute | Value |
|---|---|
| name | `txtCMultiCRegion` |
| description | `Region` |
| text | `{DT_ADDITIONAL_CCODE-REGIN}` |

#### 7.1.3 `txtCMultiCCode` — Text

| Attribute | Value |
|---|---|
| name | `txtCMultiCCode` |
| description | `Company Code Text` |
| text | `{DT_ADDITIONAL_CCODE-COMP_TEXT}` |

#### 7.1.4 `txtCMultiCCode_Text` — Text

| Attribute | Value |
|---|---|
| name | `txtCMultiCCode_Text` |
| description | `Company Code Text` |
| text | `{DT_ADDITIONAL_CCODE-COMP_TEXT}` |

> ⚠️ **Note:** `txtCMultiCCode` and `txtCMultiCCode_Text` both bind to `DT_ADDITIONAL_CCODE-COMP_TEXT` in the PDF. This may be a Neptune source duplicate/copy-paste. Verify whether `txtCMultiCCode` should bind to the code (`COMP_CODE`) and `txtCMultiCCode_Text` to the description (`COMP_TEXT`).

#### 7.1.5 `txtCMultiCostCenter` — Text

| Attribute | Value |
|---|---|
| name | `txtCMultiCostCenter` |
| description | `Cost Center Id` |
| text | `{DT_ADDITIONAL_CCODE-KOSTL_CODE}` |

#### 7.1.6 `txtCMultiCCenter_Text` — Text

| Attribute | Value |
|---|---|
| name | `txtCMultiCCenter_Text` |
| description | `Cost Center Text` |
| text | `{DT_ADDITIONAL_CCODE-KOSTL_TEXT}` |

#### 7.1.7 `btnDeleteCCode` — Button (Delete row)

| Attribute | Value |
|---|---|
| name | `btnDeleteCCode` |
| description | `Delete Multiple CCode` |
| icon | `sap-icon://delete` |
| enabled | `{DT_ADDITIONAL_CCODE-ISULTRUE}` |
| width | `75px` |

**Events:**

| Event | Code |
|---|---|
| `press` | Anonymous Function — See §8.2 |

---

## 8. EVENT HANDLERS & CONTROLLER LOGIC

### 8.1 `btnAddCompCode` — press event

```javascript
// Handler name: onAddCompCode
// Opens diaAddMultiCC dialog to add a new additional company code row
diaAddMultiCC.open();
```

**Fiori Controller equivalent:**
```javascript
onAddCompCode: function () {
    this.byId("diaAddMultiCC").open();
},
```

> The `diaAddMultiCC` dialog (defined in `oPageDialogs` in the master control tree) contains `sfAddMultiCCodes` SimpleForm with `oSelectMultiCCCode` (Select) and `inpMultiCostCenter` (Input). Its confirm button (`btnAddMultiCCOK`) saves the row to `listAttachments` / the `DT_ADDITIONAL_CCODE` model and refreshes the table.

---

### 8.2 `btnDeleteCCode` — press event

```javascript
// Handler name: onDeleteCCode
// Deletes the row from the DT_ADDITIONAL_CCODE model
// Exact code not visible in PDF — based on app pattern
ModelData.Delete(listAdditionalCCode, <binding context index>);
modeloPageProj.refresh();
```

**Fiori Controller equivalent:**
```javascript
onDeleteCCode: function (oEvent) {
    var oContext = oEvent.getSource().getBindingContext("additionalCCode");
    var oModel = this.getView().getModel("additionalCCode");
    var aData = oModel.getData();
    var iIndex = oContext.getPath().replace("/", "");
    aData.splice(parseInt(iIndex), 1);
    oModel.setData(aData);
},
```

---

### 8.3 `validate_additionalCCode()` — from `form.txt`

```javascript
function validate_additionalCCode() {
    var error = false;
    var table = document.getElementById("tabMultiCC");
    // Body appears to be a stub — no validation logic implemented
}
```

> ⚠️ This function is a **stub** in the Neptune source (only gets the DOM element, no actual validation). Port as a stub.

---

## 9. MODEL BINDINGS REFERENCE

### 9.1 `DT_ADDITIONAL_CCODE` — Additional Company Code list model

This is the table's data model. Each row in `tabMultiCC` is bound to one entry.

| Binding Path | Field | Control |
|---|---|---|
| `DT_ADDITIONAL_CCODE-STATUS` | Row status icon src | `oFormStatusIcon` src |
| `DT_ADDITIONAL_CCODE-REGIN` | Region code | `txtCMultiCRegion` text |
| `DT_ADDITIONAL_CCODE-COMP_TEXT` | Company code text | `txtCMultiCCode` text, `txtCMultiCCode_Text` text |
| `DT_ADDITIONAL_CCODE-KOSTL_CODE` | Cost center ID | `txtCMultiCostCenter` text |
| `DT_ADDITIONAL_CCODE-KOSTL_TEXT` | Cost center text | `txtCMultiCCenter_Text` text |
| `DT_ADDITIONAL_CCODE-ISULTRUE` | Delete button enabled flag | `btnDeleteCCode` enabled |
| `DT_ADDITIONAL_CCODE-PROJ_FLGRP_0001` | Add button enabled flag | `btnAddCompCode` enabled |

### 9.2 `DS_PROJ_FORM` — SubSection mode

| Binding Path | Purpose |
|---|---|
| `DS_PROJ_FORM-PROJ_FLGRP_0001_MOD` | SubSection mode (edit/display) |

### 9.3 Notes on model naming in Fiori

In Neptune, `DT_ADDITIONAL_CCODE` is the table model name. In Fiori, bind the table to a named JSON model, e.g. `additionalCCode`, and use relative binding paths:

```xml
<m:Table items="{additionalCCode>/}">
```

Each `ColumnListItem` cell then uses `{REGIN}`, `{COMP_TEXT}`, `{KOSTL_CODE}` etc. (without the `DT_ADDITIONAL_CCODE-` prefix).

---

## 10. DIALOG REFERENCED — `diaAddMultiCC`

This dialog (in `oPageDialogs` section) is opened by `btnAddCompCode`. Full spec of the dialog content:

| Control | Type | Details |
|---|---|---|
| `diaAddMultiCC` | `sap.m.Dialog` | Add additional company code |
| `sfAddMultiCCodes` | `sap.ui.layout.form.SimpleForm` | Form inside dialog |
| `lblMultiCCode` | `sap.m.Label` | text: `Company Code` *(approx)* |
| `oSelectMultiCCCode` | `sap.m.Select` | Company code dropdown |
| `lblMultiCostCenter` | `sap.m.Label` | text: `Cost Center` *(approx)* |
| `inpMultiCostCenter` | `sap.m.Input` | Cost center input |
| `btnAddMultiCCOK` | `sap.m.Button` | Confirm / OK |
| `btnAddMultiCCCancel` | `sap.m.Button` | Cancel |

> Full dialog spec is part of the `oPageDialogs` section spec (to be done separately).

---

## 11. COMPLETE FIORI XML VIEW — `subsectionProjMultiCC`

```xml
<!-- Namespaces required:
     xmlns:uxap="sap.uxap"
     xmlns:form="sap.ui.layout.form"
     xmlns:m="sap.m"
     xmlns:core="sap.ui.core"
-->

<uxap:ObjectPageSubSection
    id="subsectionProjMultiCC"
    title="Additional Company Code"
    mode="{DS_PROJ_FORM>PROJ_FLGRP_0001_MOD}"
    visible="false">

  <uxap:moreBlocks>

    <form:SimpleForm id="sfBudgetCCode" editable="false">
      <form:content>

        <m:Table
            id="tabMultiCC"
            mode="None"
            noDataText="No additional company code"
            showSeparators="Inner"
            styleClass="busy-border"
            growingThreshold="5"
            items="{additionalCCode>/}">

          <!-- Header Toolbar -->
          <m:headerToolbar>
            <m:Toolbar id="tbTabMultiCCData">
              <m:ToolbarSpacer id="tbSpacerCCode" />
              <m:Button
                  id="btnAddCompCode"
                  text="Additional Company Code"
                  icon="sap-icon://add"
                  enabled="{DT_ADDITIONAL_CCODE>PROJ_FLGRP_0001}"
                  press=".onAddCompCode" />
            </m:Toolbar>
          </m:headerToolbar>

          <!-- Column Definitions -->
          <m:columns>
            <m:Column id="colStatusICON">
              <m:header><m:Text text="Status" /></m:header>
            </m:Column>
            <m:Column id="colCRegion">
              <m:header><m:Text text="Region" /></m:header>
            </m:Column>
            <m:Column id="colCCompCode">
              <m:header><m:Text text="Company Code" /></m:header>
            </m:Column>
            <m:Column id="colCCompCode_Text">
              <m:header><m:Text text="Company Description" /></m:header>
            </m:Column>
            <m:Column id="colCostCenterCode">
              <m:header><m:Text text="Cost Center Code" /></m:header>
            </m:Column>
            <m:Column id="colCostCenterText">
              <m:header><m:Text text="Cost Center Text" /></m:header>
            </m:Column>
            <m:Column id="colCDelete">
              <m:header><m:Text text="Delete" /></m:header>
            </m:Column>
          </m:columns>

          <!-- Row Template -->
          <m:items>
            <m:ColumnListItem
                id="listItemMultiCCode"
                type="Active">
              <m:cells>
                <core:Icon
                    id="oFormStatusIcon"
                    src="{STATUS}"
                    color="Green" />
                <m:Text id="txtCMultiCRegion"   text="{REGIN}" />
                <m:Text id="txtCMultiCCode"     text="{COMP_TEXT}" />
                <m:Text id="txtCMultiCCode_Text" text="{COMP_TEXT}" />
                <m:Text id="txtCMultiCostCenter" text="{KOSTL_CODE}" />
                <m:Text id="txtCMultiCCenter_Text" text="{KOSTL_TEXT}" />
                <m:Button
                    id="btnDeleteCCode"
                    icon="sap-icon://delete"
                    enabled="{ISULTRUE}"
                    width="75px"
                    press=".onDeleteCCode" />
              </m:cells>
            </m:ColumnListItem>
          </m:items>

        </m:Table>

      </form:content>
    </form:SimpleForm>

  </uxap:moreBlocks>
</uxap:ObjectPageSubSection>
```

---

## 12. COMPLETE FIORI CONTROLLER METHODS

```javascript
// ============================================================
// subsectionProjMultiCC — Controller Methods
// ============================================================

/**
 * Add Company Code button — press.
 * Opens the add-additional-CC dialog.
 */
onAddCompCode: function () {
    this.byId("diaAddMultiCC").open();
},

/**
 * Delete CCode row button — press.
 * Removes the row from the additionalCCode model.
 */
onDeleteCCode: function (oEvent) {
    var oModel = this.getView().getModel("additionalCCode");
    var aData = oModel.getData();
    var oCtx = oEvent.getSource().getBindingContext("additionalCCode");
    var iIndex = parseInt(oCtx.getPath().replace("/", ""), 10);
    aData.splice(iIndex, 1);
    oModel.setData(aData);
},

/**
 * Validate additional company code table — stub.
 * Body empty in Neptune source — port as stub.
 */
_validateAdditionalCCode: function () {
    return false; // stub
},
```

---

## 13. OPEN QUESTIONS / MIGRATION NOTES

| # | Item | Status |
|---|---|---|
| 1 | `txtCMultiCCode` and `txtCMultiCCode_Text` both bind to `DT_ADDITIONAL_CCODE-COMP_TEXT` in the Neptune PDF. Likely a copy-paste issue — `txtCMultiCCode` probably should bind to `COMP_CODE` (the key). Verify with backend data structure | ⚠️ Verify |
| 2 | `btnAddCompCode` `icon` attribute value not clearly readable in PDF. Defaulted to `sap-icon://add` — confirm actual icon used | ❓ Open |
| 3 | `oFormStatusIcon` `color` is shown as `Green` — confirm if this is static or dynamically set based on `STATUS` value | ❓ Open |
| 4 | `subsectionProjMultiCC` starts with `visible="false"` — confirm the runtime condition that shows it (likely based on `PROJ_FLGRP_*` flag or specific project type) | ❓ Open |
| 5 | `btnDeleteCCode` `enabled` binds to `DT_ADDITIONAL_CCODE-ISULTRUE` — confirm this is a boolean field returned by backend per row | ❓ Open |
| 6 | `validate_additionalCCode()` is a stub in Neptune source — confirm whether any real row-level validation is needed before submit | ❓ Open |
| 7 | The `diaAddMultiCC` dialog full spec (its `oSelectMultiCCCode` model source, `inpMultiCostCenter` binding, and both button handlers) should be covered in the `oPageDialogs` spec | 📋 Pending |
| 8 | In Fiori, the `additionalCCode` model name needs to be agreed on — it maps to Neptune's `DT_ADDITIONAL_CCODE` table model | ✅ Plan |
| 9 | `growingThreshold=5` means the table shows 5 rows initially with a "More" button — replicate in Fiori with `growing="true"` and `growingThreshold="5"` | ✅ Confirmed |
