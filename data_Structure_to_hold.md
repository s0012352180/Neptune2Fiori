# Neptune Data Folder — Model Structures Specification

> **Source PDF:** `data_Structure_to_hold.pdf`  
> **Purpose:** Documents the 7 Neptune "Data" folder objects — transient model containers that hold values exchanged between the UI and backend AJAX calls. None are added to the view. This spec explains what each structure is, what it maps to, and exactly how to replicate the pattern in SAP Fiori freestyle and SAP RAP (backend).

---

## 1. What the Neptune "Data Folder" Is

In Neptune, the **Data folder** under Resources contains model objects declared with a backend structure or table type as their Model Source. These objects:

- Are **not rendered in the view** (all marked "Do not add to View, only declare")
- Act as **named JSON model containers** in the frontend JavaScript
- Are populated by AJAX `Receive` parameters and read by AJAX `Send` parameters
- Serve as the **single source of truth** for a given data domain at runtime
- Persist across AJAX calls within the same session (in-memory, not persisted)

**Fiori equivalent:** Named `sap.ui.model.json.JSONModel` instances created in the controller's `onInit()` and set on the view with `this.getView().setModel(oModel, "modelName")`.

---

## 2. Complete Data Structure Inventory

| # | Name                  | Control Type       | Model Source              | ABAP Type  | Role                                      |
|---|-----------------------|--------------------|---------------------------|------------|-------------------------------------------|
| 1 | `AttachmentCategories`| `neptune.model.array` | `GT_ATTACHMENT_CATEGORIES` | Table     | Lookup list for attachment category dropdown |
| 2 | `AttachmentsRequired` | `neptune.model.array` | `GT_ATTACHMENTS_REQUIRED`  | Table     | Rules defining which attachments are required per project type |
| 3 | `CompanyCodes`        | `sap.m.List`       | `GT_COMP_CODE`            | Table      | Company code lookup list                  |
| 4 | `ProgramState`        | `sap.m.FlexBox`    | `WA_PROGRAM_STATE`        | Structure  | Global UI state flags (display/edit mode, visibility toggles) |
| 5 | `cntr_formdetails`    | `sap.m.FlexBox`    | `GS_FORMDETAILS`          | Structure  | Form header metadata (approval step, form GUID, status) |
| 6 | `cntr_stepdetails`    | `sap.m.FlexBox`    | `GS_STEPDETAIL`           | Structure  | Current workflow step details             |
| 7 | `UrlParameters`       | `sap.m.FlexBox`    | `WA_URL_PARAMETERS`       | Structure  | URL/launch parameters (FORM_GUID, MODE, PROJ, FROM) |

---

## 3. Individual Structure Details

### 3.1 `AttachmentCategories`
| Property     | Value                       |
|--------------|-----------------------------|
| Control Type | `neptune.model.array`       |
| Model Source | `GT_ATTACHMENT_CATEGORIES`  |
| ABAP Type    | Table type (array)          |
| Ajax         | REST Transformation (BETA)  |

**Purpose:** Populates the attachment category `<Select>` dropdown inside `diaAddAttachment`. Each entry has a key and display text for an attachment category (e.g. "FP&A Approval Document", "CAPEX Software License").

**Used by:** `diaAddAttachment` dialog, `validateAttachment()` in `form.txt`, `visible_additional_field()` in `Utilities.txt`.

**Populated by:** `GETDETAIL` AJAX — Receive parameter `GT_ATTACHMENT_CATEGORIES`.

---

### 3.2 `AttachmentsRequired`
| Property     | Value                       |
|--------------|-----------------------------|
| Control Type | `neptune.model.array`       |
| Model Source | `GT_ATTACHMENTS_REQUIRED`   |
| ABAP Type    | Table type (array)          |
| Ajax         | REST Transformation (BETA)  |

**Purpose:** Defines which attachment categories are mandatory for each `PROJECT_TYPE` + `PROJECT_SUBTYPE` combination. Used to drive attachment validation logic and visibility of attachment upload sections.

**Used by:** `validateAttachment()` in `form.txt`, `visible_additional_field()` in `Utilities.txt`. Both functions call `ModelData.Find(AttachmentsRequired, [...], [...], [...])`.

**Populated by:** `GETDETAIL` AJAX — Receive parameter `GT_ATTACHMENTS_REQUIRED`.

**Structure fields (inferred from usage):**

| Field            | Type    | Description                          |
|------------------|---------|--------------------------------------|
| `PROJECT_TYPE`   | String  | Project type key                     |
| `PROJECT_SUBTYPE`| String  | Project subtype key (default `"00"`) |
| `ATT_CATEGORY`   | String  | Category code (e.g. `ACCOUNTING`)    |
| `REQUIRED`       | Boolean | Whether the attachment is mandatory  |

---

### 3.3 `CompanyCodes`
| Property     | Value           |
|--------------|-----------------|
| Control Type | `sap.m.List`    |
| Model Source | `GT_COMP_CODE`  |
| ABAP Type    | Table type      |
| Ajax         | *(not set)*     |

**Purpose:** Company code lookup list. Provides the data source for company code selection controls on the form.

**Note:** Declared as `sap.m.List` in Neptune (unlike the others which use FlexBox/array). This means it was originally intended as a list-bound model; in Fiori treat it as a standard JSONModel array.

**Populated by:** `GETDETAIL` AJAX — Receive parameter `GT_COMP_CODE`.

---

### 3.4 `ProgramState`
| Property     | Value              |
|--------------|--------------------|
| Control Type | `sap.m.FlexBox`    |
| Model Source | `WA_PROGRAM_STATE` |
| ABAP Type    | Structure (WA)     |
| Ajax         | *(not set)*        |

**Purpose:** Global UI state flags. Controls visibility and edit/display mode across the entire form. Read and written by multiple controller functions.

**Used by:** `setUI()`, `toggleMode()`, `form.txt` visibility logic.

**Populated by:** `GETDETAIL` AJAX — Receive parameter `WA_PROGRAM_STATE`.

**Structure fields (inferred from usage across source files):**

| Field             | Type    | Description                                 |
|-------------------|---------|---------------------------------------------|
| `DISPLAY_OR_EDIT` | String  | `"DISPLAY"` or `"EDIT"` — current form mode |
| `CAN_ATTACH`      | Boolean | Controls `btnAddAttachment` visibility      |
| `CAN_SUBMIT`      | Boolean | Submit button visibility                    |
| `CAN_CHANGE`      | Boolean | Change button visibility                    |
| `CAN_REJECT`      | Boolean | Reject button visibility                    |
| `CAN_CLOSE`       | Boolean | Close/Withdraw button visibility            |

---

### 3.5 `cntr_formdetails`
| Property     | Value            |
|--------------|------------------|
| Control Type | `sap.m.FlexBox`  |
| Model Source | `GS_FORMDETAILS` |
| ABAP Type    | Structure (GS)   |
| Ajax         | *(not set)*      |

**Purpose:** Form-level metadata including the current approval step, form GUID, and workflow status. Drives dynamic behaviour like initiative group filtering (`updateinitgrp()` in `Utilities.txt`).

**Used by:** `updateinitgrp()` — checks `modelcntr_formdetails.oData.APPR_STEP === "FPA"`.

**Populated by:** `GETDETAIL` AJAX — Receive parameter `GS_FORMDETAILS`.

**Structure fields (inferred from usage):**

| Field        | Type   | Description                                      |
|--------------|--------|--------------------------------------------------|
| `APPR_STEP`  | String | Current approval step code (e.g. `"FPA"`, `"RTR"`) |
| `FORM_GUID`  | String | Unique form identifier                           |
| `STATUS`     | String | Current form status code                         |

---

### 3.6 `cntr_stepdetails`
| Property     | Value            |
|--------------|------------------|
| Control Type | `sap.m.FlexBox`  |
| Model Source | `GS_STEPDETAIL`  |
| ABAP Type    | Structure (GS)   |
| Ajax         | *(not set)*      |

**Purpose:** Current workflow step details. Contains step-specific data used to control which actions (approve, reject, etc.) are available at each point in the approval chain.

**Populated by:** `GETDETAIL` AJAX — Receive parameter `GS_STEPDETAIL`.

---

### 3.7 `UrlParameters`
| Property     | Value               |
|--------------|---------------------|
| Control Type | `sap.m.FlexBox`     |
| Model Source | `WA_URL_PARAMETERS` |
| ABAP Type    | Structure (WA)      |
| Ajax         | *(not set)*         |

**Purpose:** Holds URL/launch parameters passed when the app opens. Read by `getUrlParameters()` in `Utilities.txt`, written by `loadRequest()`, `toggleMode()`, and `loadProject()`.

**Used by:** `getUrlParameters()`, `toggleMode()`, `loadProject()`, `getOnlineGetDetail()` (sent as AJAX Send parameters).

**Structure fields (confirmed from `form.txt` and `Utilities.txt`):**

| Field        | Type   | Description                                       |
|--------------|--------|---------------------------------------------------|
| `FORM_GUID`  | String | Form GUID from URL parameter `form_guid`          |
| `MODE`       | String | `""`, `"DISPLAY"`, `"CHANGE"` — launch mode       |
| `PROJ`       | String | Project ID                                        |
| `FROM`       | String | Navigation source (e.g. `"search"`)               |
| `PROCESSING_REGION` | String | Processing region passed to GETDETAIL    |
| `COMPANY_CODE` | String | Company code passed to GETDETAIL               |

---

## 4. How Neptune Uses These Models (AJAX Flow)

```
App Launch
    │
    ├─► getUrlParameters()          ← reads URL → writes modelUrlParameters
    │
    └─► getOnlineGetDetail()        ← AJAX SEND: modelUrlParameters fields
             │
             └─► GETDETAIL AJAX
                  │
                  RECEIVE → populates all Data models:
                  ├── AttachmentCategories  (GT_ATTACHMENT_CATEGORIES)
                  ├── AttachmentsRequired   (GT_ATTACHMENTS_REQUIRED)
                  ├── CompanyCodes          (GT_COMP_CODE)
                  ├── ProgramState          (WA_PROGRAM_STATE)
                  ├── cntr_formdetails      (GS_FORMDETAILS)
                  ├── cntr_stepdetails      (GS_STEPDETAIL)
                  └── modeloPageProj        (GS_PROJ_FORM — main form data)
```

After `GETDETAIL` completes, all models are populated and the UI renders with the correct data, visibility, and edit state.

---

## 5. Fiori Freestyle — Implementation Pattern

### 5.1 Model Declaration in `onInit()`

```javascript
onInit: function () {
    // Declare all Data-folder equivalent models
    this.getView().setModel(new sap.ui.model.json.JSONModel([]),  "AttachmentCategories");
    this.getView().setModel(new sap.ui.model.json.JSONModel([]),  "AttachmentsRequired");
    this.getView().setModel(new sap.ui.model.json.JSONModel([]),  "CompanyCodes");
    this.getView().setModel(new sap.ui.model.json.JSONModel({}),  "ProgramState");
    this.getView().setModel(new sap.ui.model.json.JSONModel({}),  "cntr_formdetails");
    this.getView().setModel(new sap.ui.model.json.JSONModel({}),  "cntr_stepdetails");
    this.getView().setModel(new sap.ui.model.json.JSONModel({
        FORM_GUID: "",
        MODE:      "",
        PROJ:      "",
        FROM:      "",
        PROCESSING_REGION: "",
        COMPANY_CODE: ""
    }), "modelUrlParameters");

    // Also declare main form model
    this.getView().setModel(new sap.ui.model.json.JSONModel({}),  "modeloPageProj");
},
```

### 5.2 Populating Models from AJAX Response

In Fiori, AJAX responses come back as OData or REST JSON. Map the response fields to each named model:

```javascript
_onGetDetailSuccess: function (oResponse) {
    var oView = this.getView();

    // Table-type models — set as arrays
    oView.getModel("AttachmentCategories").setData(oResponse.GT_ATTACHMENT_CATEGORIES || []);
    oView.getModel("AttachmentsRequired").setData(oResponse.GT_ATTACHMENTS_REQUIRED   || []);
    oView.getModel("CompanyCodes").setData(oResponse.GT_COMP_CODE                     || []);

    // Structure models — set as objects
    oView.getModel("ProgramState").setData(oResponse.WA_PROGRAM_STATE    || {});
    oView.getModel("cntr_formdetails").setData(oResponse.GS_FORMDETAILS  || {});
    oView.getModel("cntr_stepdetails").setData(oResponse.GS_STEPDETAIL   || {});

    // Main form model
    oView.getModel("modeloPageProj").setData(oResponse.GS_PROJ_FORM      || {});

    // Trigger post-load UI setup
    this._setUI();
    this._setProcessingRegion();
    this._visibleAdditionalField();
},
```

### 5.3 Reading Model Values in Controller

```javascript
// Neptune pattern:
var step = modelcntr_formdetails.oData.APPR_STEP;

// Fiori equivalent:
var step = this.getView().getModel("cntr_formdetails").getProperty("/APPR_STEP");

// Neptune pattern:
var mode = modeloPageProj.oData.DISPLAY_OR_EDIT;

// Fiori equivalent:
var mode = this.getView().getModel("modeloPageProj").getProperty("/DISPLAY_OR_EDIT");
```

### 5.4 Sending Model Values in AJAX (OData)

```javascript
// Build the Send payload from model properties
var oUrlParams  = this.getView().getModel("modelUrlParameters").getData();
var oPageModel  = this.getView().getModel("modeloPageProj").getData();

var oPayload = {
    FORM_GUID:         oUrlParams.FORM_GUID,
    MODE:              oUrlParams.MODE,
    PROJ:              oUrlParams.PROJ,
    PROCESSING_REGION: oPageModel.PROCESSING_REGION,
    COMPANY_CODE:      oPageModel.COMPANY_CODE
};

// Call OData / REST endpoint
this._callGetDetail(oPayload);
```

---

## 6. SAP RAP Backend — Mapping

When building the RAP backend, each Neptune model source maps to an ABAP artifact:

| Neptune Model Source        | ABAP Type    | RAP Equivalent                                      |
|-----------------------------|--------------|-----------------------------------------------------|
| `GT_ATTACHMENT_CATEGORIES`  | Table type   | Internal table in action/function import response   |
| `GT_ATTACHMENTS_REQUIRED`   | Table type   | Internal table in action/function import response   |
| `GT_COMP_CODE`              | Table type   | Internal table (value help provider)                |
| `WA_PROGRAM_STATE`          | Structure    | Field in response structure — UI state flags        |
| `GS_FORMDETAILS`            | Structure    | Field in response structure — form header data      |
| `GS_STEPDETAIL`             | Structure    | Field in response structure — workflow step data    |
| `WA_URL_PARAMETERS`         | Structure    | Input parameter structure of `GETDETAIL` function   |

### RAP Response Structure (suggested)

```abap
" Response structure for GETDETAIL action
TYPES: BEGIN OF ty_getdetail_response,
    gs_proj_form              TYPE zs_proj_form,
    wa_program_state          TYPE zwa_program_state,
    gs_formdetails            TYPE zgs_formdetails,
    gs_stepdetail             TYPE zgs_stepdetail,
    gt_attachment_categories  TYPE STANDARD TABLE OF zgt_att_categories WITH DEFAULT KEY,
    gt_attachments_required   TYPE STANDARD TABLE OF zgt_att_required WITH DEFAULT KEY,
    gt_comp_code              TYPE STANDARD TABLE OF zgt_comp_code WITH DEFAULT KEY,
END OF ty_getdetail_response.
```

### RAP Input Structure (suggested)

```abap
" Input structure for GETDETAIL action
TYPES: BEGIN OF ty_getdetail_input,
    form_guid          TYPE zguid,
    mode               TYPE zmode,           " DISPLAY / CHANGE / (empty)
    proj               TYPE zprojid,
    from               TYPE zfrom,
    processing_region  TYPE zregion,
    company_code       TYPE bukrs,
END OF ty_getdetail_input.
```

---

## 7. Key Principles for Migration

1. **One JSONModel per Neptune Data object** — Each item in the Neptune Data folder becomes a named `sap.ui.model.json.JSONModel` on the Fiori view. Use the same name as the Neptune object for traceability.

2. **Arrays vs. structures** — Neptune array models (`AttachmentCategories`, `AttachmentsRequired`, `CompanyCodes`) → initialise with `[]`. Neptune structure models (`ProgramState`, `cntr_formdetails`, `cntr_stepdetails`, `UrlParameters`) → initialise with `{}`.

3. **"Do not add to View"** — These models are never bound to XML view controls by path. They are only read/written programmatically in the controller. In Fiori, set them on the view but don't bind view controls directly to them (except `modeloPageProj` which IS bound to view controls).

4. **`ModelData.Find()` → JavaScript `Array.filter()`** — Neptune's custom `ModelData.Find(array, fields, values, operators)` is used extensively on `AttachmentsRequired` and `AttachmentCategories`. Replace with standard JS:
    ```javascript
    // Neptune:
    var required = ModelData.Find(AttachmentsRequired,
        ["PROJECT_TYPE","PROJECT_SUBTYPE","REQUIRED"], [type, subtype, true], ["EQ","EQ","EQ"]);
    
    // Fiori equivalent:
    var aRequired = this.getView().getModel("AttachmentsRequired").getData();
    var required = aRequired.filter(function(r) {
        return r.PROJECT_TYPE === type && r.PROJECT_SUBTYPE === subtype && r.REQUIRED === true;
    });
    ```

5. **`modeloPageProj.oData.X` → `getProperty("/X")`** — All direct property reads in Neptune (`model.oData.FIELD`) must be replaced with the Fiori model API (`model.getProperty("/FIELD")`). Direct writes (`model.oData.FIELD = value; model.refresh()`) become `model.setProperty("/FIELD", value)`.

6. **`UrlParameters` is both input and state** — This model is unique: it is written at launch from the URL, then written again by navigation functions (`toggleMode`, `loadProject`), and read as the Send payload for `GETDETAIL`. In Fiori, treat it as a controller-level state model that is always sent with every `GETDETAIL` call.
