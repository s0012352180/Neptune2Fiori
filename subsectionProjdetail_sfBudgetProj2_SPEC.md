# Component Spec: `subsectionProjdetail` → `sfBudgetProj2`
## Neptune → SAP Fiori Freestyle Migration
### Application: `ZDCI_NAD_PROJECT_REQUEST`

---

## 1. COMPONENT OVERVIEW

| Property | Value |
|---|---|
| **SimpleForm ID** | `sfBudgetProj2` |
| **Parent Block** | `ProjDetailBlock` (position: `MoreBlock`) |
| **Parent SubSection** | `subsectionProjdetail` |
| **Sibling** | `sfBudgetProj` (previous spec) |
| **Layout** | `sap.ui.layout.form.SimpleForm` |

> `sfBudgetProj2` contains all the **location/organisational fields** — Company Code, Cost Center, Location, US State, Brand, Geography, Cap Number, Work Order Date, Completion Date, and the FPA Approval Document attachment row.

---

## 2. COMPLETE CONTROL HIERARCHY — `sfBudgetProj2`

```
sfBudgetProj2  [sap.ui.layout.form.SimpleForm]
  ├── lblCompanyCode       [sap.m.Label]
  ├── oSelectCompCode      [sap.m.Select]
  ├── lblCostCenter        [sap.m.Label]
  ├── inpCostCenter        [sap.m.Input]  ← value-help only, opens diaSearchCostCenter
  ├── lblLocation          [sap.m.Label]
  ├── inpLocation          [sap.m.Input]  ← value-help only, opens diaLocation
  ├── oSelectUSState       [sap.m.Select]
  ├── lblBrand             [sap.m.Label]
  ├── inpBrand             [sap.m.Input]  ← value-help only, opens diaBrand
  ├── lblGeography         [sap.m.Label]
  ├── inpGeography         [sap.m.Input]  ← value-help only, opens diaGeography
  ├── lblCapNumber         [sap.m.Label]
  ├── inpCapNumber         [sap.m.Input]
  ├── lblWorkOrderDate     [sap.m.Label]
  ├── dporderdate          [sap.m.DatePicker]
  ├── lblCompletionDt      [sap.m.Label]
  ├── dpOrderCompDate      [sap.m.DatePicker]
  ├── lblFPAApprovalAtt    [sap.m.Label]
  └── vbFPAApprovalAtt     [sap.m.HBox / VBox]
        └── btnFPAApprovalAtt  [sap.m.Button]
```

---

## 3. SIMPLEFORM ATTRIBUTES

### 3.1 `sfBudgetProj2` — SimpleForm

| Attribute | Value |
|---|---|
| name | `sfBudgetProj2` |
| description | *(blank)* |
| Model Source | *(none — uses parent page model)* |
| Ajax ID | *(blank)* |

> No explicit `editable`, `layout`, `labelSpanL/M` observed in the PDF screenshot for sfBudgetProj2 — inherit same settings as `sfBudgetProj`: `editable=true`, `layout=ResponsiveGridLayout`, `labelSpanL=12`, `labelSpanM=12`.

---

## 4. CONTROL SPECIFICATIONS — ALL FIELDS

### 4.1 `lblCompanyCode` — Label

| Attribute | Value |
|---|---|
| name | `lblCompanyCode` |
| description | `Company Code` |
| text | `Company Code` |
| required | `{GS_PROJ_FORM-R_COMPANY_CODE}` |
| visible | `{GS_PROJ_FORM-V_COMPANY_CODE}` |

### 4.2 `oSelectCompCode` — Select

| Attribute | Value |
|---|---|
| name | `oSelectCompCode` |
| description | `Company Code` |
| enabled | `{GS_PROJ_FORM-PROJ_FLGRP_0001}` |
| setEnableCache | `true` |
| setInitLoad | `cache` |

**Model Source (Application Class):**

| Property | Value |
|---|---|
| Model Source | `GT_COMP_CODE` |
| Value Field | `VALUE` |
| Label Field | `TEXT` |

**Events:**

| Event | Code |
|---|---|
| `change` | See §5.1 |

---

### 4.3 `lblCostCenter` — Label

| Attribute | Value |
|---|---|
| name | `lblCostCenter` |
| description | `Cost Center` |
| text | `Cost Center` |
| required | `{GS_PROJ_FORM-R_KOSTL}` |
| visible | `{GS_PROJ_FORM-V_KOSTL}` |

### 4.4 `inpCostCenter` — Input

| Attribute | Value |
|---|---|
| name | `inpCostCenter` |
| description | `CostCenter` |
| value | `{GS_PROJ_FORM-KOSTL_TEXT}` |
| enabled | `{GS_PROJ_FORM-PROJ_FLGRP_0001}` |
| showValueHelp | `true` |
| valueHelpOnly | `true` |
| showSuggestion | `true` |
| visible | `{GS_PROJ_FORM-V_KOSTL}` |

**Events:**

| Event | Code |
|---|---|
| `valueHelpRequest` | See §5.2 |

> ⚠️ **Note:** The `inpCostCenter.valueHelpRequest` handler includes a guard: company code must be selected first. If not selected, it shows an error message instead of opening the dialog. See §5.2 for full code.

---

### 4.5 `lblLocation` — Label

| Attribute | Value |
|---|---|
| name | `lblLocation` |
| description | *(blank)* |
| text | `Location` |
| required | `{GS_PROJ_FORM-R_LOCATION}` |

### 4.6 `inpLocation` — Input

| Attribute | Value |
|---|---|
| name | `inpLocation` |
| description | *(blank)* |
| value | `{GS_PROJ_FORM-LOCATION}` |
| enabled | `{GS_PROJ_FORM-PROJ_FLGRP_0001}` |
| maxLength | `10` |
| showValueHelp | `true` |
| valueHelpOnly | `true` |
| visible | `{GS_PROJ_FORM-V_LOCATION}` |

**Events:**

| Event | Code |
|---|---|
| `valueHelpRequest` | `diaLocation.open();` |

---

### 4.7 `oSelectUSState` — Select

| Attribute | Value |
|---|---|
| name | `oSelectUSState` |
| description | *(blank — visible in tree as red/highlighted item)* |
| enabled | `{GS_PROJ_FORM-PROJ_FLGRP_0001}` |

> `oSelectUSState` appears in the control tree between `inpLocation` and `lblBrand`. No model source or explicit binding visible in the PDF screenshots — marked as open question (§12).

---

### 4.8 `lblBrand` — Label

| Attribute | Value |
|---|---|
| name | `lblBrand` |
| description | *(blank)* |
| text | `Brand` |
| required | `{GS_PROJ_FORM-R_BRAND}` |

### 4.9 `inpBrand` — Input

| Attribute | Value |
|---|---|
| name | `inpBrand` |
| description | *(blank)* |
| value | `{GS_PROJ_FORM-BRAND}` |
| enabled | `{GS_PROJ_FORM-PROJ_FLGRP_0001}` |
| maxLength | `5` |
| showValueHelp | `true` |
| valueHelpOnly | `true` |
| visible | `{GS_PROJ_FORM-V_BRAND}` |

**Events:**

| Event | Code |
|---|---|
| `valueHelpRequest` | `diaBrand.open();` |

---

### 4.10 `lblGeography` — Label

| Attribute | Value |
|---|---|
| name | `lblGeography` |
| description | *(blank)* |
| text | `Geography` |
| required | `{GS_PROJ_FORM-R_GEOGRAPHY}` |

### 4.11 `inpGeography` — Input

| Attribute | Value |
|---|---|
| name | `inpGeography` |
| description | *(blank)* |
| enabled | `{GS_PROJ_FORM-PROJ_FLGRP_0001}` |
| maxLength | `4` |
| showValueHelp | `true` |
| valueHelpOnly | `true` |

**Events:**

| Event | Code |
|---|---|
| `valueHelpRequest` | `diaGeography.open();` |

---

### 4.12 `lblCapNumber` — Label

| Attribute | Value |
|---|---|
| name | `lblCapNumber` |
| description | *(blank)* |
| text | `Cap Number` |
| required | `{GS_PROJ_FORM-R_CAPD}` |

### 4.13 `inpCapNumber` — Input

| Attribute | Value |
|---|---|
| name | `inpCapNumber` |
| description | *(blank)* |
| enabled | `{GS_PROJ_FORM-PROJ_FLGRP_0001}` |

**Events:** None defined.

---

### 4.14 `lblWorkOrderDate` — Label

| Attribute | Value |
|---|---|
| name | `lblWorkOrderDate` |
| description | `Work Order Date` |
| text | `Order Date` |
| required | `{GS_PROJ_FORM-R_ORDER_DATE}` |
| visible | `{GS_PROJ_FORM-V_ORDER_DATE}` |

### 4.15 `dporderdate` — DatePicker

| Attribute | Value |
|---|---|
| name | `dporderdate` |
| description | `Order Date` |
| value | `{GS_PROJ_FORM-ORDER_DATE}` |
| valueFormat | `yyyyMMdd` |
| displayFormat | `date:SAPUsedDefault` *(SAP default locale date format)* |
| styleClass | `one-line-form-field` |
| enabled | `{GS_PROJ_FORM-PROJ_FLGRP_0001}` |
| visible | `{GS_PROJ_FORM-V_ORDER_DATE}` |

**Events:** None defined.

---

### 4.16 `lblCompletionDt` — Label

| Attribute | Value |
|---|---|
| name | `lblCompletionDt` |
| description | `Completion Date` |
| text | `Project Completion Date (estimated)` |
| required | `{GS_PROJ_FORM-R_PROJ_COMPLETION_DATE}` |
| visible | `{GS_PROJ_FORM-V_PROJ_COMPLETION_DATE}` |
| wrapping | `true` |

### 4.17 `dpOrderCompDate` — DatePicker

| Attribute | Value |
|---|---|
| name | `dpOrderCompDate` |
| description | `Project Completion Date (estimated)` |
| value | `{GS_PROJ_FORM-PROJ_COMPLETION_DATE}` |
| valueFormat | `yyyyMMdd` |
| displayFormat | `date:SAPUsedDefault` |
| styleClass | `one-line-form-field` |
| enabled | `{GS_PROJ_FORM-PROJ_FLGRP_0001}` |
| visible | `{GS_PROJ_FORM-V_PROJ_COMPLETION_DATE}` |

**Events:** None defined (date validation handled by `validate_date()` — see §6).

---

### 4.18 `lblFPAApprovalAtt` — Label

| Attribute | Value |
|---|---|
| name | `lblFPAApprovalAtt` |
| description | *(blank)* |
| text | `FP&A Approval Document` |
| required | `true` *(hardcoded)* |

### 4.19 `vbFPAApprovalAtt` — HBox / VBox container

| Attribute | Value |
|---|---|
| name | `vbFPAApprovalAtt` |
| description | *(blank)* |

**Child:**

#### 4.19.1 `btnFPAApprovalAtt` — Button

| Attribute | Value |
|---|---|
| name | `btnFPAApprovalAtt` |
| description | *(blank)* |
| text | `Upload FP&A Approval Documents` |
| icon | `sap-icon://attachment` |
| visible | `{DS_PROJ_FORM-CAN_ATTACH}` |

**Events:**

| Event | Code |
|---|---|
| `press` | Anonymous Function — see §5.3 |

---

## 5. EVENT HANDLERS & CONTROLLER LOGIC

### 5.1 `oSelectCompCode` — change event

```javascript
// Handler name: onCompanyCodeChange
var companycode = oSelectCompCode.getSelectedKey();

if (companycode) {
    modelUrlParameters.setProperty("/COMPANY_CODE", companycode);
    getOnlineGetCostCenter('*');
    // Clear Cost Center if it's selected before
    inpCostCenter.setValue("");
    modeloPageProj.oData.KOSTL = "";
    modeloPageProj.refresh();
    inpCostCenter.fireChange();
}
```

**Fiori Controller equivalent:**
```javascript
onCompanyCodeChange: function () {
    var oSelect = this.byId("oSelectCompCode");
    var sCompCode = oSelect.getSelectedKey();
    if (sCompCode) {
        this.getView().getModel("urlParameters").setProperty("/COMPANY_CODE", sCompCode);
        this._getOnlineGetCostCenter("*");
        // Clear Cost Center
        this.byId("inpCostCenter").setValue("");
        this.getView().getModel("pageProj").setProperty("/KOSTL", "");
        this.byId("inpCostCenter").fireChange();
    }
},
```

> **Side effect:** Calling `getOnlineGetCostCenter('*')` reloads the cost center list filtered by the new company code. The `inpCostCenter.fireChange()` call ensures any downstream change handlers on cost center are triggered after the clear.

---

### 5.2 `inpCostCenter` — valueHelpRequest event

**Full code (two stages captured from PDF pages 3–5):**

**Stage A** — inline comment code (page 3):
```javascript
//////////// ;
// (guard logic leads to):
var companycode = oSelectCompCode.getSelectedKey();
if (companycode) {
    modelUrlParameters.setProperty("/COMPANY_CODE", companycode);
    diaSearchCostCenter.open();
} else {
    discoveryFiori.showMessage(
        "Company code is required to select Cost Center",
        sap.ui.core.ValueState.Error
    );
}
```

**Fiori Controller equivalent:**
```javascript
onCostCenterValueHelp: function () {
    var sCompCode = this.byId("oSelectCompCode").getSelectedKey();
    if (sCompCode) {
        this.getView().getModel("urlParameters").setProperty("/COMPANY_CODE", sCompCode);
        this.byId("diaSearchCostCenter").open();
    } else {
        sap.m.MessageBox.error(
            "Company code is required to select Cost Center"
        );
    }
},
```

> ⚠️ **Critical dependency:** The cost center search dialog (`diaSearchCostCenter`) requires `COMPANY_CODE` to be set on `urlParameters` model before opening, because the dialog's backend call uses it to filter results.

---

### 5.3 `btnFPAApprovalAtt` — press event

The button opens the file upload dialog for FPA approval documents. Based on the Neptune pattern for attachment upload (from `Utilities.txt` / Init.txt — `fu = new discoveryFiori.FileUploader(...)`):

```javascript
// Handler name: onFPAApprovalAttPress
// Opens the add-attachment dialog, scoped to FPA category
fu.open("FPA_APPROVAL");   // or equivalent Neptune file uploader call
```

> The exact press handler code was not visible in the PDF (anonymous function). Based on the app pattern, it should open `diaAddAttachment` and set the attachment category context. Confirm with the AJAX `SAVE` parameters for attachment category.

**Fiori Controller equivalent:**
```javascript
onFPAApprovalAttPress: function () {
    // Open attachment upload dialog for FPA approval category
    this.byId("diaAddAttachment").open();
    // Set category context if needed
},
```

---

### 5.4 `inpLocation` — valueHelpRequest event

```javascript
// Handler name: onLocationValueHelp
diaLocation.open();
```

**Fiori Controller equivalent:**
```javascript
onLocationValueHelp: function () {
    this.byId("diaLocation").open();
},
```

---

### 5.5 `inpBrand` — valueHelpRequest event

```javascript
// Handler name: onBrandValueHelp
diaBrand.open();
```

**Fiori Controller equivalent:**
```javascript
onBrandValueHelp: function () {
    this.byId("diaBrand").open();
},
```

---

### 5.6 `inpGeography` — valueHelpRequest event

```javascript
// Handler name: onGeographyValueHelp
diaGeography.open();
```

**Fiori Controller equivalent:**
```javascript
onGeographyValueHelp: function () {
    this.byId("diaGeography").open();
},
```

---

## 6. VALIDATION LOGIC FOR THIS FORM

### 6.1 Standard required field validation

`sfBudgetProj2` is added to `currform` (validated) under the same condition as `sfBudgetProj`:
```javascript
if (modeloPageProj.oData.PROJ_FLGRP_0001 || modeloPageProj.oData.PROJ_FLGRP_0005) {
    currform.push(sfBudgetProj);
    currform.push(sfBudgetProj2);
}
```

### 6.2 `validate_date()` — Date range validation (from `form.txt`)

This function validates the two DatePickers in `sfBudgetProj2`:

```javascript
function validate_date() {
    var error = false;
    if (dpOrderCompDate.getDateValue() < dporderdate.getDateValue()) {
        dpOrderCompDate.setValueState(sap.ui.core.ValueState.Error);
        dpOrderCompDate.setValueStateText(
            "The Project Completion Date (estimated) is less than Order Date");
        dpOrderCompDate.setShowValueStateMessage(true);
        dpOrderCompDate.focus();
        error = true;
    }
    return error;
}
```

**Fiori Controller equivalent:**
```javascript
_validateDate: function () {
    var oCompDate  = this.byId("dpOrderCompDate");
    var oOrderDate = this.byId("dporderdate");
    if (oCompDate.getDateValue() < oOrderDate.getDateValue()) {
        oCompDate.setValueState(sap.ui.core.ValueState.Error);
        oCompDate.setValueStateText(
            "The Project Completion Date (estimated) is less than Order Date");
        oCompDate.setShowValueStateMessage(true);
        oCompDate.focus();
        return true;
    }
    return false;
},
```

> **Called before submit** alongside `validateRequiredAll()`, `validateAmount()`, `validateAttachment()`, and `validate_bussjustname()`.

### 6.3 Invalid date format validation (from `form.txt`)

For all `sap.m.DatePicker` controls — even non-required ones — if `_bValid === false` (user typed an invalid date), validation sets `Error` state:

```javascript
} else if (field._bValid === false) {
    if (field instanceof sap.m.DatePicker) {
        field.setValueState(sap.ui.core.ValueState.Error);
        field.setValueStateText("Invalid date.");
        field.setShowValueStateMessage(true);
        field.focus();
        error = true;
    }
}
```

Both `dporderdate` and `dpOrderCompDate` are subject to this check even when not required.

---

## 7. MODEL BINDINGS REFERENCE

### 7.1 `GS_PROJ_FORM` — Main form model bindings

| Binding Path | Field | Control |
|---|---|---|
| `COMPANY_CODE` *(via Select key)* | Company Code | `oSelectCompCode` selectedKey |
| `KOSTL` | Cost Center code | Written to on clear; read in cost center dialog return |
| `KOSTL_TEXT` | Cost Center display text | `inpCostCenter` value |
| `LOCATION` | Location code | `inpLocation` value |
| `BRAND` | Brand code | `inpBrand` value |
| `ORDER_DATE` | Work Order Date | `dporderdate` value |
| `PROJ_COMPLETION_DATE` | Project Completion Date | `dpOrderCompDate` value |
| `PROJ_FLGRP_0001` | Edit flag | `enabled` on all inputs / selects / datepickers |

### 7.2 Visibility Flags

| Binding Path | Controls Affected |
|---|---|
| `V_COMPANY_CODE` | `lblCompanyCode` visible |
| `V_KOSTL` | `lblCostCenter`, `inpCostCenter` visible |
| `V_LOCATION` | `inpLocation` visible |
| `V_BRAND` | `inpBrand` visible |
| `V_ORDER_DATE` | `lblWorkOrderDate`, `dporderdate` visible |
| `V_PROJ_COMPLETION_DATE` | `lblCompletionDt`, `dpOrderCompDate` visible |

### 7.3 Required Flags

| Binding Path | Label |
|---|---|
| `R_COMPANY_CODE` | `lblCompanyCode` |
| `R_KOSTL` | `lblCostCenter` |
| `R_LOCATION` | `lblLocation` |
| `R_BRAND` | `lblBrand` |
| `R_GEOGRAPHY` | `lblGeography` |
| `R_CAPD` | `lblCapNumber` |
| `R_ORDER_DATE` | `lblWorkOrderDate` |
| `R_PROJ_COMPLETION_DATE` | `lblCompletionDt` |
| *(hardcoded `true`)* | `lblFPAApprovalAtt` |

### 7.4 `DS_PROJ_FORM` bindings

| Binding Path | Control | Purpose |
|---|---|---|
| `DS_PROJ_FORM-CAN_ATTACH` | `btnFPAApprovalAtt` visible | Controls whether upload button is shown |

### 7.5 `modelUrlParameters` bindings (set programmatically)

| Property | Set By | Value |
|---|---|---|
| `/COMPANY_CODE` | `oSelectCompCode.change` | Selected company code key |
| `/COMPANY_CODE` | `inpCostCenter.valueHelpRequest` | Confirmed company code before opening dialog |

### 7.6 External Data Models (for Select dropdowns)

| Model Name | Used By | Source Type |
|---|---|---|
| `GT_COMP_CODE` | `oSelectCompCode` | Application Class, Value=VALUE, Label=TEXT, cached |

---

## 8. DIALOGS REFERENCED BY THIS FORM

| Dialog ID | Opened By | Purpose |
|---|---|---|
| `diaSearchCostCenter` | `inpCostCenter` valueHelpRequest | Cost center search (requires company code in urlParameters) |
| `diaLocation` | `inpLocation` valueHelpRequest | Location lookup |
| `diaBrand` | `inpBrand` valueHelpRequest | Brand lookup |
| `diaGeography` | `inpGeography` valueHelpRequest | Geography lookup |
| `diaAddAttachment` | `btnFPAApprovalAtt` press | File upload for FPA approval document |

---

## 9. AJAX CALLS TRIGGERED BY THIS FORM

| Neptune Function | Trigger | Purpose | Fiori Method |
|---|---|---|---|
| `getOnlineGetCostCenter('*')` | `oSelectCompCode.change` | Loads cost centers for selected company code | `_getOnlineGetCostCenter(sFilter)` |

---

## 10. COMPLETE FIORI XML VIEW — `sfBudgetProj2`

```xml
<!-- Part of subsectionProjdetail > ProjDetailBlock > uxap:moreBlocks -->

<form:SimpleForm
    id="sfBudgetProj2"
    editable="true"
    layout="ResponsiveGridLayout"
    labelSpanL="12"
    labelSpanM="12">
  <form:content>

    <!-- Company Code -->
    <m:Label id="lblCompanyCode"
        text="Company Code"
        required="{pageProj>R_COMPANY_CODE}"
        visible="{pageProj>V_COMPANY_CODE}" />
    <m:Select id="oSelectCompCode"
        enabled="{pageProj>PROJ_FLGRP_0001}"
        change=".onCompanyCodeChange">
      <!-- Items bound to GT_COMP_CODE model -->
    </m:Select>

    <!-- Cost Center -->
    <m:Label id="lblCostCenter"
        text="Cost Center"
        required="{pageProj>R_KOSTL}"
        visible="{pageProj>V_KOSTL}" />
    <m:Input id="inpCostCenter"
        value="{pageProj>KOSTL_TEXT}"
        enabled="{pageProj>PROJ_FLGRP_0001}"
        showValueHelp="true"
        valueHelpOnly="true"
        showSuggestion="true"
        visible="{pageProj>V_KOSTL}"
        valueHelpRequest=".onCostCenterValueHelp" />

    <!-- Location -->
    <m:Label id="lblLocation"
        text="Location"
        required="{pageProj>R_LOCATION}" />
    <m:Input id="inpLocation"
        value="{pageProj>LOCATION}"
        enabled="{pageProj>PROJ_FLGRP_0001}"
        maxLength="10"
        showValueHelp="true"
        valueHelpOnly="true"
        visible="{pageProj>V_LOCATION}"
        valueHelpRequest=".onLocationValueHelp" />

    <!-- US State -->
    <m:Select id="oSelectUSState"
        enabled="{pageProj>PROJ_FLGRP_0001}">
      <!-- Items TBD — model source not captured in PDF -->
    </m:Select>

    <!-- Brand -->
    <m:Label id="lblBrand"
        text="Brand"
        required="{pageProj>R_BRAND}" />
    <m:Input id="inpBrand"
        value="{pageProj>BRAND}"
        enabled="{pageProj>PROJ_FLGRP_0001}"
        maxLength="5"
        showValueHelp="true"
        valueHelpOnly="true"
        visible="{pageProj>V_BRAND}"
        valueHelpRequest=".onBrandValueHelp" />

    <!-- Geography -->
    <m:Label id="lblGeography"
        text="Geography"
        required="{pageProj>R_GEOGRAPHY}" />
    <m:Input id="inpGeography"
        enabled="{pageProj>PROJ_FLGRP_0001}"
        maxLength="4"
        showValueHelp="true"
        valueHelpOnly="true"
        valueHelpRequest=".onGeographyValueHelp" />

    <!-- Cap Number -->
    <m:Label id="lblCapNumber"
        text="Cap Number"
        required="{pageProj>R_CAPD}" />
    <m:Input id="inpCapNumber"
        enabled="{pageProj>PROJ_FLGRP_0001}" />

    <!-- Work Order Date -->
    <m:Label id="lblWorkOrderDate"
        text="Order Date"
        required="{pageProj>R_ORDER_DATE}"
        visible="{pageProj>V_ORDER_DATE}" />
    <m:DatePicker id="dporderdate"
        value="{pageProj>ORDER_DATE}"
        valueFormat="yyyyMMdd"
        styleClass="one-line-form-field"
        enabled="{pageProj>PROJ_FLGRP_0001}"
        visible="{pageProj>V_ORDER_DATE}" />

    <!-- Project Completion Date -->
    <m:Label id="lblCompletionDt"
        text="Project Completion Date (estimated)"
        required="{pageProj>R_PROJ_COMPLETION_DATE}"
        visible="{pageProj>V_PROJ_COMPLETION_DATE}"
        wrapping="true" />
    <m:DatePicker id="dpOrderCompDate"
        value="{pageProj>PROJ_COMPLETION_DATE}"
        valueFormat="yyyyMMdd"
        styleClass="one-line-form-field"
        enabled="{pageProj>PROJ_FLGRP_0001}"
        visible="{pageProj>V_PROJ_COMPLETION_DATE}" />

    <!-- FP&A Approval Document -->
    <m:Label id="lblFPAApprovalAtt"
        text="FP&amp;A Approval Document"
        required="true" />
    <m:HBox id="vbFPAApprovalAtt">
      <m:Button id="btnFPAApprovalAtt"
          text="Upload FP&amp;A Approval Documents"
          icon="sap-icon://attachment"
          visible="{DS_PROJ_FORM>CAN_ATTACH}"
          press=".onFPAApprovalAttPress" />
    </m:HBox>

  </form:content>
</form:SimpleForm>
```

---

## 11. COMPLETE FIORI CONTROLLER METHODS

```javascript
// ============================================================
// sfBudgetProj2 — Controller Methods
// ============================================================

/**
 * Company Code select — change.
 * Reloads cost center list; clears previously selected cost center.
 */
onCompanyCodeChange: function () {
    var sCompCode = this.byId("oSelectCompCode").getSelectedKey();
    if (sCompCode) {
        this.getView().getModel("urlParameters").setProperty("/COMPANY_CODE", sCompCode);
        this._getOnlineGetCostCenter("*");
        // Clear previously selected cost center
        this.byId("inpCostCenter").setValue("");
        this.getView().getModel("pageProj").setProperty("/KOSTL", "");
        this.byId("inpCostCenter").fireChange();
    }
},

/**
 * Cost Center input — value help request.
 * Requires company code to be selected first.
 */
onCostCenterValueHelp: function () {
    var sCompCode = this.byId("oSelectCompCode").getSelectedKey();
    if (sCompCode) {
        this.getView().getModel("urlParameters").setProperty("/COMPANY_CODE", sCompCode);
        this.byId("diaSearchCostCenter").open();
    } else {
        sap.m.MessageBox.error(
            "Company code is required to select Cost Center"
        );
    }
},

/**
 * Location input — value help request.
 */
onLocationValueHelp: function () {
    this.byId("diaLocation").open();
},

/**
 * Brand input — value help request.
 */
onBrandValueHelp: function () {
    this.byId("diaBrand").open();
},

/**
 * Geography input — value help request.
 */
onGeographyValueHelp: function () {
    this.byId("diaGeography").open();
},

/**
 * FP&A Approval Document upload button — press.
 */
onFPAApprovalAttPress: function () {
    this.byId("diaAddAttachment").open();
},

/**
 * Date range validation — called before submit.
 * Completion date must be >= Order date.
 */
_validateDate: function () {
    var oCompDate  = this.byId("dpOrderCompDate");
    var oOrderDate = this.byId("dporderdate");
    if (oCompDate.getDateValue() && oOrderDate.getDateValue() &&
        oCompDate.getDateValue() < oOrderDate.getDateValue()) {
        oCompDate.setValueState(sap.ui.core.ValueState.Error);
        oCompDate.setValueStateText(
            "The Project Completion Date (estimated) is less than Order Date");
        oCompDate.setShowValueStateMessage(true);
        oCompDate.focus();
        return true;
    }
    return false;
},

/**
 * Private: Load cost center list from backend.
 * Called after company code changes.
 */
_getOnlineGetCostCenter: function (sFilter) {
    // Implementation: call AJAX object GET_COST_CENTER or equivalent
    // passing COMPANY_CODE from urlParameters model
},
```

---

## 12. OPEN QUESTIONS / MIGRATION NOTES

| # | Item | Status |
|---|---|---|
| 1 | `oSelectUSState` — model source, binding path, and label not captured in the PDF. The control appears highlighted (red) in the Neptune tree which may indicate it was conditionally visible or had a data-binding issue. Needs confirmation of its model source and when it is visible | ❓ Open — needs PDF |
| 2 | `inpGeography` — value binding not visible in PDF screenshots (only `enabled` and events captured). Confirm the model path (likely `{pageProj>GEOGRAPHY}`) | ❓ Open |
| 3 | `inpCapNumber` — no value binding, no events visible in PDF. Confirm model path (likely `{pageProj>CAPNO}` or similar) | ❓ Open |
| 4 | `btnFPAApprovalAtt` press handler is an anonymous function — exact code not visible in PDF. Confirm whether it opens `diaAddAttachment` with a category pre-set or uses a different mechanism | ❓ Open |
| 5 | `sfBudgetProj2` has no explicit `editable` / `layout` / `labelSpan` in the PDF — confirm these inherit same values as `sfBudgetProj` | ⚠️ Verify |
| 6 | `dporderdate` and `dpOrderCompDate` `displayFormat` shown as `date:SAPUsedDefault` in Neptune — in Fiori use `displayFormat="medium"` or locale-aware binding | ⚠️ Verify |
| 7 | `inpCostCenter` binds `value` to `KOSTL_TEXT` (display text) but `oData.KOSTL` holds the key — ensure both are cleared/set correctly when the cost center dialog returns a selection | ⚠️ Critical |
| 8 | `vbFPAApprovalAtt` visibility is controlled by `visible_additional_field()` for `PROJECT_TYPE = "6"` (hidden) — this must also hide `lblFPAApprovalAtt`. Confirm both label and container are hidden together | ⚠️ Critical |
| 9 | `inpCostCenter.fireChange()` is called after clearing value — ensure the Fiori equivalent does not accidentally trigger backend calls with empty value | ⚠️ Verify |
| 10 | `oSelectCompCode` model `GT_COMP_CODE` — confirm if loaded as part of GETDETAIL response or as a separate cached call | ❓ Open |
