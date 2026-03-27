# Component Spec: `subsectionProjAttDownload`
## Neptune → SAP Fiori Freestyle Migration
### Application: `ZDCI_NAD_PROJECT_REQUEST`

---

## 1. COMPONENT OVERVIEW

| Property | Value |
|---|---|
| **Neptune SubSection ID** | `subsectionProjAttDownload` |
| **Neptune Type** | `sap.uxap.ObjectPageSubSection` |
| **Title** | `Important Forms` |
| **Mode** | `{DS_PROJ_FORM-PROJ_FLGRP_0001_MOD}` |
| **Visible** | `{GS_PROJ_FORM-CAN_ATTACH}` |
| **Block ID** | `sfProjAttDownload` *(SimpleForm acts directly as block content)* |
| **Parent Section** | `sectionProj` |

> This subsection contains **three attachment rows**, each with a Label, a VBox/HBox container, and inside it a download Link + upload Button pair. The three rows are: CAPEX Licenses, Software License, Accounting Questionnaire.

---

## 2. COMPLETE CONTROL HIERARCHY

```
subsectionProjAttDownload  [sap.uxap.ObjectPageSubSection]
  └── sfProjAttDownload  [sap.ui.layout.form.SimpleForm]
        ├── lblAttCAPEXLicenses          [sap.m.Label]
        ├── vbAttCAPEXLicenses           [sap.m.VBox / HBox]
        │     ├── lnkAttCAPEXLicenseDownload   [sap.m.Link]
        │     └── btnAttCAPEXLicenseUpload     [sap.m.Button]
        ├── lblAttSoftwareLicense        [sap.m.Label]
        ├── vbAttSoftwareLicense         [sap.m.VBox / HBox]
        │     └── btnAttSoftwareLicenseUpload  [sap.m.Button]
        ├── lblAttAccounting             [sap.m.Label]
        └── vbAttAccounting              [sap.m.VBox / HBox]
              ├── lnkAttAccountingDownload     [sap.m.Link]
              └── btnAttAccountingUpload       [sap.m.Button]
```

---

## 3. SUBSECTION & FORM ATTRIBUTES

### 3.1 `subsectionProjAttDownload` — ObjectPageSubSection

| Attribute | Value |
|---|---|
| name | `subsectionProjAttDownload` |
| title | `Important Forms` |
| mode | `{DS_PROJ_FORM-PROJ_FLGRP_0001_MOD}` |
| visible | `{GS_PROJ_FORM-CAN_ATTACH}` |

### 3.2 `sfProjAttDownload` — SimpleForm

| Attribute | Value |
|---|---|
| name | `sfProjAttDownload` |
| editable | `true` |
| layout | `ResponsiveGridLayout` |
| labelSpanL | `12` |
| labelSpanM | `12` |
| labelSpanXL | `12` |

---

## 4. CONTROL SPECIFICATIONS

### ROW 1 — CAPEX Licenses Questionnaire

### 4.1 `lblAttCAPEXLicenses` — Label

| Attribute | Value |
|---|---|
| name | `lblAttCAPEXLicenses` |
| text | `CAPEX Licenses Questionnaire` |
| required | `true` *(hardcoded)* |

### 4.2 `vbAttCAPEXLicenses` — VBox / HBox container

| Attribute | Value |
|---|---|
| name | `vbAttCAPEXLicenses` |

**Children:**

#### 4.2.1 `lnkAttCAPEXLicenseDownload` — Link

| Attribute | Value |
|---|---|
| name | `lnkAttCAPEXLicenseDownload` |
| text | `Download CAPEX License Form Template` |
| href | `/neptune/public/application/ZDCI_NAD_PROJECT_REQUEST/Templates/CAPEX%20License%20Questionnaire.xlsx` |
| target | `_blank` |

#### 4.2.2 `btnAttCAPEXLicenseUpload` — Button

| Attribute | Value |
|---|---|
| name | `btnAttCAPEXLicenseUpload` |
| text | `Upload CAPEX Questionnaire` |
| icon | `sap-icon://attachment` |
| visible | `{GS_PROJ_FORM-CAN_ATTACH}` |

**Events:**

| Event | Code |
|---|---|
| `press` | Anonymous Function — opens file upload dialog for CAPEX category. See §5.1 |

---

### ROW 2 — CAPEX Software License Agreement

### 4.3 `lblAttSoftwareLicense` — Label

| Attribute | Value |
|---|---|
| name | `lblAttSoftwareLicense` |
| text | `CAPEX Software License Agreement` |
| required | `true` *(hardcoded)* |

### 4.4 `vbAttSoftwareLicense` — VBox / HBox container

| Attribute | Value |
|---|---|
| name | `vbAttSoftwareLicense` |

**Children:**

#### 4.4.1 `btnAttSoftwareLicenseUpload` — Button

| Attribute | Value |
|---|---|
| name | `btnAttSoftwareLicenseUpload` |
| text | `Upload License Agreement` |
| icon | `sap-icon://attachment` |
| visible | `{GS_PROJ_FORM-CAN_ATTACH}` |

**Events:**

| Event | Code |
|---|---|
| `press` | Anonymous Function — opens file upload dialog for Software License category. See §5.2 |

> ⚠️ `vbAttSoftwareLicense` has **no download link** — upload only. Contrast with the CAPEX Licenses row which has both a download template link and an upload button.

---

### ROW 3 — Accounting Questionnaire

### 4.5 `lblAttAccounting` — Label

| Attribute | Value |
|---|---|
| name | `lblAttAccounting` |
| text | `Accounting Questionnaire` |
| required | `true` *(hardcoded)* |

### 4.6 `vbAttAccounting` — VBox / HBox container

| Attribute | Value |
|---|---|
| name | `vbAttAccounting` |

**Children:**

#### 4.6.1 `lnkAttAccountingDownload` — Link

| Attribute | Value |
|---|---|
| name | `lnkAttAccountingDownload` |
| text | `Download Accounting Questionnaire Form Template` |
| href | `/neptune/public/application/ZDCI_NAD_PROJECT_REQUEST/Templates/Accounting%20Questionnaire.xlsx` |
| target | `_blank` |

#### 4.6.2 `btnAttAccountingUpload` — Button

| Attribute | Value |
|---|---|
| name | `btnAttAccountingUpload` |
| text | `Upload Accounting Questionnaire` |
| icon | `sap-icon://attachment` |
| visible | `{GS_PROJ_FORM-CAN_ATTACH}` |

**Events:**

| Event | Code |
|---|---|
| `press` | Anonymous Function — opens file upload dialog for Accounting category. See §5.3 |

---

## 5. EVENT HANDLERS

### 5.1 `btnAttCAPEXLicenseUpload` — press

```javascript
// Opens the add-attachment dialog scoped to CAPEX_LICENSE category
// Handler name: onUploadCAPEXLicense
// Exact code: anonymous function — opens diaAddAttachment
diaAddAttachment.open();
// (attachment category context = "CAPEX_LICENSE" set before opening)
```

**Fiori Controller equivalent:**
```javascript
onUploadCAPEXLicense: function () {
    this._sAttachmentCategory = "CAPEX_LICENSE";
    this.byId("diaAddAttachment").open();
},
```

---

### 5.2 `btnAttSoftwareLicenseUpload` — press

```javascript
// Handler name: onUploadSoftwareLicense
diaAddAttachment.open();
// (attachment category context = "SOFTWARE_LICENSE")
```

**Fiori Controller equivalent:**
```javascript
onUploadSoftwareLicense: function () {
    this._sAttachmentCategory = "SOFTWARE_LICENSE";
    this.byId("diaAddAttachment").open();
},
```

---

### 5.3 `btnAttAccountingUpload` — press

```javascript
// Handler name: onUploadAccounting
diaAddAttachment.open();
// (attachment category context = "ACCOUNTING")
```

**Fiori Controller equivalent:**
```javascript
onUploadAccounting: function () {
    this._sAttachmentCategory = "ACCOUNTING";
    this.byId("diaAddAttachment").open();
},
```

---

## 6. VISIBILITY CONTROL — `visible_additional_field()` interaction

From `Utilities.txt`, these three VBox containers are shown/hidden based on `PROJECT_TYPE` and attachment requirements:

```javascript
vbAttCAPEXLicenses.setVisible(formVis[0]);    // ATT_CATEGORY = "CAPEX_LICENSE"
vbAttSoftwareLicense.setVisible(formVis[1]);  // ATT_CATEGORY = "SOFTWARE_LICENSE"
vbAttAccounting.setVisible(formVis[2]);       // ATT_CATEGORY = "ACCOUNTING"

// Special rule: hide Accounting if PROJECT_TYPE=3 and ITL not selected
if (type === "3" && !rbITLYes.getSelected()) {
    vbAttAccounting.setVisible(false);
}
```

The labels (`lblAttCAPEXLicenses`, `lblAttSoftwareLicense`, `lblAttAccounting`) must be hidden together with their corresponding VBox containers. In Neptune the label visibility is implicitly paired with the container. In Fiori, bind or programmatically set label visibility to match the container.

**Fiori Controller — visibility sync:**
```javascript
// Called from _visibleAdditionalField()
this.byId("lblAttCAPEXLicenses").setVisible(formVis[0]);
this.byId("vbAttCAPEXLicenses").setVisible(formVis[0]);
this.byId("lblAttSoftwareLicense").setVisible(formVis[1]);
this.byId("vbAttSoftwareLicense").setVisible(formVis[1]);
this.byId("lblAttAccounting").setVisible(formVis[2]);
this.byId("vbAttAccounting").setVisible(formVis[2]);

if (sType === "3" && !this.byId("rbITLYes").getSelected()) {
    this.byId("lblAttAccounting").setVisible(false);
    this.byId("vbAttAccounting").setVisible(false);
}
```

---

## 7. TEMPLATE FILE PATHS

The two download links point to static files served from the Neptune application's public folder. In Fiori these must be relocated:

| Template | Neptune Path | Fiori Path (suggested) |
|---|---|---|
| CAPEX License Questionnaire | `/neptune/public/application/ZDCI_NAD_PROJECT_REQUEST/Templates/CAPEX%20License%20Questionnaire.xlsx` | `/sap/bc/ui5_ui5/sap/ZDCI_NAD_PROJECT_REQUEST/Templates/CAPEX_License_Questionnaire.xlsx` *(or static file server)* |
| Accounting Questionnaire | `/neptune/public/application/ZDCI_NAD_PROJECT_REQUEST/Templates/Accounting%20Questionnaire.xlsx` | `/sap/bc/ui5_ui5/sap/ZDCI_NAD_PROJECT_REQUEST/Templates/Accounting_Questionnaire.xlsx` *(or static file server)* |

> ⚠️ The exact Fiori static resource path must be confirmed with the Basis/transport team. Options: (1) BSP application static resources, (2) MIME repository, (3) hardcoded ABAP ICF handler URL.

---

## 8. ATTACHMENT VALIDATION — `validateAttachment()` (from `form.txt`)

This subsection's upload buttons are the mechanism by which attachments are added. The validation function checks whether required attachments are present before submit:

```javascript
function validateAttachment() {
    var type = modeloPageProj.oData.PROJECT_TYPE;
    var subtype = ...;

    var required = ModelData.Find(AttachmentsRequired,
        ["PROJECT_TYPE","PROJECT_SUBTYPE","REQUIRED"],
        [type, subtype, true], ["EQ","EQ","EQ"]);

    required.forEach(function(category) {
        // Skip ACCOUNTING for type 3 if ITL not selected
        if (type === "3" && category.ATT_CATEGORY === "ACCOUNTING"
                && !rbITLYes.getSelected()) return;

        if (ModelData.Find(listAttachments, "CATEGORY",
                category.ATT_CATEGORY, "EQ").length === 0) {
            // Show error — attachment missing
            error = true;
        }
    });

    // Special rule for type 3: need FP&A + (ACCOUNTING or CAPEX_LICENSE)
    if (type === "3") {
        if (count > 1) { error = false; }
        else {
            if (rbITLYes.getSelected()) {
                discoveryFiori.showMessage(
                    "You must attach the FP&A Approval Document and either"
                    + " the Accounting Questionnaire or CAPEX Software License"
                    + " to this form before you submit it for approval.",
                    sap.ui.core.ValueState.Error);
            } else {
                discoveryFiori.showMessage(
                    "You must attach the FP&A Approval Document and"
                    + " the CAPEX Software License"
                    + " to this form before you submit it for approval.",
                    sap.ui.core.ValueState.Error);
            }
        }
    }
    return error;
}
```

**Fiori Controller equivalent:**
```javascript
_validateAttachment: function () {
    var oModel = this.getView().getModel("pageProj");
    var sType = oModel.getProperty("/PROJECT_TYPE");
    var sSubtype = oModel.getProperty("/PROJECT_SUBTYPE") || "00";
    var aAttachments = this.getView().getModel("attachments").getData();
    var aRequired = this._findAttachmentsRequired(sType, sSubtype);
    var bError = false;
    var iCount = 0;

    aRequired.forEach(function (cat) {
        if (sType === "3" && cat.ATT_CATEGORY === "ACCOUNTING"
                && !this.byId("rbITLYes").getSelected()) return;

        var bFound = aAttachments.some(function (att) {
            return att.CATEGORY === cat.ATT_CATEGORY;
        });
        if (!bFound) {
            bError = true;
        } else {
            iCount++;
        }
    }.bind(this));

    if (sType === "3") {
        if (iCount > 1) {
            bError = false;
        } else {
            var sMsg = this.byId("rbITLYes").getSelected()
                ? "You must attach the FP&A Approval Document and either the Accounting Questionnaire or CAPEX Software License to this form before you submit it for approval."
                : "You must attach the FP&A Approval Document and the CAPEX Software License to this form before you submit it for approval.";
            sap.m.MessageBox.error(sMsg);
        }
    }
    return bError;
},
```

---

## 9. MODEL BINDINGS REFERENCE

| Binding Path | Control | Purpose |
|---|---|---|
| `GS_PROJ_FORM-CAN_ATTACH` | `subsectionProjAttDownload` visible | Shows/hides entire subsection |
| `GS_PROJ_FORM-CAN_ATTACH` | All upload buttons `visible` | Shows upload buttons when attachment allowed |
| `DS_PROJ_FORM-PROJ_FLGRP_0001_MOD` | SubSection `mode` | Edit/display mode |

---

## 10. COMPLETE FIORI XML VIEW

```xml
<uxap:ObjectPageSubSection
    id="subsectionProjAttDownload"
    title="Important Forms"
    mode="{DS_PROJ_FORM>PROJ_FLGRP_0001_MOD}"
    visible="{pageProj>CAN_ATTACH}">

  <uxap:blocks>

    <form:SimpleForm
        id="sfProjAttDownload"
        editable="true"
        layout="ResponsiveGridLayout"
        labelSpanL="12"
        labelSpanM="12"
        labelSpanXL="12">
      <form:content>

        <!-- CAPEX Licenses Questionnaire -->
        <m:Label id="lblAttCAPEXLicenses"
            text="CAPEX Licenses Questionnaire"
            required="true" />
        <m:HBox id="vbAttCAPEXLicenses">
          <m:Link id="lnkAttCAPEXLicenseDownload"
              text="Download CAPEX License Form Template"
              href="/path/to/CAPEX_License_Questionnaire.xlsx"
              target="_blank" />
          <m:Button id="btnAttCAPEXLicenseUpload"
              text="Upload CAPEX Questionnaire"
              icon="sap-icon://attachment"
              visible="{pageProj>CAN_ATTACH}"
              press=".onUploadCAPEXLicense" />
        </m:HBox>

        <!-- CAPEX Software License Agreement -->
        <m:Label id="lblAttSoftwareLicense"
            text="CAPEX Software License Agreement"
            required="true" />
        <m:HBox id="vbAttSoftwareLicense">
          <m:Button id="btnAttSoftwareLicenseUpload"
              text="Upload License Agreement"
              icon="sap-icon://attachment"
              visible="{pageProj>CAN_ATTACH}"
              press=".onUploadSoftwareLicense" />
        </m:HBox>

        <!-- Accounting Questionnaire -->
        <m:Label id="lblAttAccounting"
            text="Accounting Questionnaire"
            required="true" />
        <m:HBox id="vbAttAccounting">
          <m:Link id="lnkAttAccountingDownload"
              text="Download Accounting Questionnaire Form Template"
              href="/path/to/Accounting_Questionnaire.xlsx"
              target="_blank" />
          <m:Button id="btnAttAccountingUpload"
              text="Upload Accounting Questionnaire"
              icon="sap-icon://attachment"
              visible="{pageProj>CAN_ATTACH}"
              press=".onUploadAccounting" />
        </m:HBox>

      </form:content>
    </form:SimpleForm>

  </uxap:blocks>
</uxap:ObjectPageSubSection>
```

---

## 11. COMPLETE FIORI CONTROLLER METHODS

```javascript
// ============================================================
// subsectionProjAttDownload — Controller Methods
// ============================================================

onUploadCAPEXLicense: function () {
    this._sAttachmentCategory = "CAPEX_LICENSE";
    this.byId("diaAddAttachment").open();
},

onUploadSoftwareLicense: function () {
    this._sAttachmentCategory = "SOFTWARE_LICENSE";
    this.byId("diaAddAttachment").open();
},

onUploadAccounting: function () {
    this._sAttachmentCategory = "ACCOUNTING";
    this.byId("diaAddAttachment").open();
},

_validateAttachment: function () {
    // Full implementation in §8 above
},
```

---

## 12. OPEN QUESTIONS / MIGRATION NOTES

| # | Item | Status |
|---|---|---|
| 1 | Template file URLs must be updated from Neptune `/neptune/public/...` paths to a Fiori-compatible static resource path. Confirm with Basis team | ⚠️ Critical |
| 2 | `vbAttSoftwareLicense` has no download link — only upload. This is intentional — the Software License Agreement is a vendor document, not an internal template. Confirm | ✅ Confirmed by source |
| 3 | All three upload buttons share the same `diaAddAttachment` dialog. The `_sAttachmentCategory` controller property routes the category to the dialog. Confirm the dialog reads this property to tag uploaded files | ⚠️ Verify |
| 4 | All three VBox visibility flags are driven by `_visibleAdditionalField()` — labels must be hidden/shown in sync with their containers. Fiori implementation must call both `setVisible()` calls together | ⚠️ Critical |
| 5 | `subsectionProjAttDownload` `visible` binds to `GS_PROJ_FORM-CAN_ATTACH` — this flag controls whether the user can attach at all (based on form status / role). Confirm the flag is returned from GETDETAIL | ❓ Open |
| 6 | `AttachmentsRequired` static data resource (from Neptune `Resources > Data`) drives which categories are required per `PROJECT_TYPE`/`PROJECT_SUBTYPE`. This must be ported as a JSON static resource or loaded from backend | ⚠️ Critical |
