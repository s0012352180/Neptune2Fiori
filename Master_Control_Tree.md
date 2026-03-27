# ZDCI_NAD_PROJECT_REQUEST — Master Control Tree Specification

> **Source PDF:** `Screenshot_of_Project_Code_Request_application_built_in_Neptune__1_.pdf`  
> **Purpose:** Complete control hierarchy of the Neptune application — the single authoritative reference for building the SAP Fiori freestyle equivalent. Controls are listed in exact Neptune render order. Use this document as the build blueprint.

---

## HOW TO USE THIS DOCUMENT

When Claude helps build the Fiori application, work **section by section, top to bottom**, in this order:

1. App Shell & Page structure
2. Object Page Header
3. `sectionProj` → `subsectionRequester` (spec: `subsectionRequester_SPEC.md`)
4. `sectionProj` → `subsectionProjdetail` (spec: `subsectionProjdetail_SPEC.md`)
5. `subsectionProjAttDownload`
6. `subsectionProjMultiCC`
7. `sectionApproval` → FPA / RTR / BPC subsections
8. `sectionAttachments`
9. `sectionApprovals`
10. `sectionComments`
11. Footer toolbar (action buttons)
12. All dialogs (see individual dialog specs)

Cross-reference each control with its individual spec file listed in Section 7.

---

## 1. App Shell

```
oShell  (sap.m.Shell)
└── oApp  (sap.m.App)
      ├── oBusyIndicator  (sap.m.BusyDialog or sap.ui.core.BusyIndicator)
      └── oPageProj  (sap.m.Page)  ← top-level page container
```

---

## 2. Page: `oPageProj`

### 2.1 Header: `headerPageProj`

```
headerPageProj
├── Left
│   └── btnNavBack          (sap.m.Button)  — back navigation; hidden unless subcomponent
└── Middle
    └── titlePagePCreate    (sap.m.Text)    — page title
```

### 2.2 Content: `oplForm` (ObjectPageLayout)

```
oplForm  (sap.uxap.ObjectPageLayout)
├── ophProj  (ObjectPageHeader)
│   ├── btnPrevOrder        (sap.m.Button)  — navigate to previous project
│   ├── btnToggleEdit       (sap.m.Button)  — toggle display/edit mode
│   └── btnNextOrder        (sap.m.Button)  — navigate to next project
│
├── hcAttributes  (ObjectPageHeaderContent)
│   ├── vlAttributesBlock1  (sap.ui.layout.VerticalLayout)
│   │   ├── oObjectEmpName  (sap.m.ObjectAttribute)  — requester employee name
│   │   └── attrStartDate   (sap.m.ObjectAttribute)  — form start date
│   └── vlAttributesBlock2  (sap.ui.layout.VerticalLayout)
│       ├── attrCurrentStep (sap.m.ObjectAttribute)  — current approval step
│       └── oObjectAgent    (sap.m.ObjectAttribute)  — current agent/approver
```

---

## 3. Section: `sectionProj`

### 3.1 Subsection: `subsectionRequester`

> **Full spec:** `subsectionRequester_SPEC.md`

```
subsectionRequester
└── RequesterBlock  (ObjectPageSubSection block)
    ├── sfBudgetManagerBlock1  (sap.ui.layout.form.SimpleForm)
    │   ├── lblQProcessingRegion  (sap.m.Label)
    │   ├── rbgQProcessingRegion  (sap.m.RadioButtonGroup)
    │   │   ├── rbUsLa    (sap.m.RadioButton)  — US/LA
    │   │   ├── rbEurope  (sap.m.RadioButton)  — Europe
    │   │   └── rbAsia    (sap.m.RadioButton)  — Asia
    │   ├── lblQReqName   (sap.m.Label)
    │   ├── inpQReqName   (sap.m.Input — Value Help)  — requester name
    │   ├── lblOnBehalf   (sap.m.Label)
    │   └── oHBoxOnBehalf (sap.m.HBox)
    │       ├── inpOnBehalfOf    (sap.m.Input — Value Help)  — on behalf of person
    │       └── CbtnOnBehalfClr (sap.m.Button)  — clear on behalf field
    │
    └── sfBudgetManagerBlock2  (sap.ui.layout.form.SimpleForm)
        ├── lblPersonResponsible  (sap.m.Label)
        ├── inpPersonResponsible  (sap.m.Input — Value Help)
        ├── oHBoxAuthBy           (sap.m.HBox)
        │   [inpAuthBy + clear button — inferred from spec]
        ├── lblAuthDate           (sap.m.Label)
        └── dpAuthDate            (sap.m.DatePicker)
```

---

### 3.2 Subsection: `subsectionProjdetail`

> **Full spec:** `subsectionProjdetail_SPEC.md`

```
subsectionProjdetail
└── ProjDetailBlock  (ObjectPageSubSection block)
    ├── sfBudgetProj  (sap.ui.layout.form.SimpleForm)
    │   ├── lblQProjName        (sap.m.Label)
    │   ├── inpProjectName      (sap.m.Input)
    │   ├── lblProjDesc         (sap.m.Label)
    │   ├── oTxtProjDesc        (sap.m.TextArea)
    │   ├── lblBussJust         (sap.m.Label)
    │   ├── oTxtProjBussJust    (sap.m.TextArea)
    │   ├── lblProjectType      (sap.m.Label)
    │   ├── oSelectProjType     (sap.m.Select)
    │   │
    │   ├── oLblTIFundReq       (sap.m.Label)    — TI Fund Required label
    │   ├── rbTIFunds           (sap.m.RadioButtonGroup)
    │   │   ├── rbTI_Fund_Yes   (sap.m.RadioButton)
    │   │   └── rbTI_Fund_No    (sap.m.RadioButton)
    │   │
    │   ├── oLblTIAmtReq        (sap.m.Label)    — TI Amount Required label
    │   ├── rbTIAmount          (sap.m.RadioButtonGroup)
    │   │   ├── rbTI_Amount_Yes (sap.m.RadioButton)
    │   │   └── rbTI_Amount_No  (sap.m.RadioButton)
    │   │
    │   ├── lblProjectSubtype   (sap.m.Label)
    │   ├── selProjectSubtype   (sap.m.Select)
    │   │
    │   ├── oLblSoftwareReq     (sap.m.Label)
    │   ├── rbSoftwareReq       (sap.m.RadioButtonGroup)
    │   │   ├── rbSoftwareYes   (sap.m.RadioButton)
    │   │   └── rbSoftwareNo    (sap.m.RadioButton)
    │   │
    │   ├── oLblITL             (sap.m.Label)
    │   ├── rbITL               (sap.m.RadioButtonGroup)
    │   │   ├── rbITLYes        (sap.m.RadioButton)
    │   │   └── rbITLNo         (sap.m.RadioButton)
    │   │
    │   ├── oLblCLS             (sap.m.Label)
    │   ├── rbCLS               (sap.m.RadioButtonGroup)
    │   │   ├── rbCLSYes        (sap.m.RadioButton)
    │   │   └── rbCLSNo         (sap.m.RadioButton)
    │   │
    │   ├── lblPrvProjCode      (sap.m.Label)
    │   ├── oSelectPrevPCode    (sap.m.Select)
    │   │
    │   ├── oLblCAPEX_AMOUNT    (sap.m.Label)
    │   ├── inpCAPEX_Amount     (sap.m.Input)
    │   ├── oLblCAPEX_CURR      (sap.m.Label)
    │   ├── oSelectCAPCURR      (sap.m.Select)
    │   ├── oLblITL_AMOUNT      (sap.m.Label)
    │   ├── inpITL_Amount       (sap.m.Input)
    │   ├── oLblITL_CURR        (sap.m.Label)
    │   └── oSelectITLCURR      (sap.m.Select)
    │
    └── sfBudgetProj2  (sap.ui.layout.form.SimpleForm)
        ├── lblCompanyCode      (sap.m.Label)
        ├── oSelectCompCode     (sap.m.Select)
        ├── lblCostCenter       (sap.m.Label)
        ├── inpCostCenter       (sap.m.Input — Value Help)
        ├── lblLocation         (sap.m.Label)
        ├── inpLocation         (sap.m.Input — Value Help)
        ├── oSelectUSState      (sap.m.Select)   ← highlighted red in Neptune (review required)
        ├── lblBrand            (sap.m.Label)
        ├── inpBrand            (sap.m.Input — Value Help)
        ├── lblGeography        (sap.m.Label)
        ├── inpGeography        (sap.m.Input — Value Help)
        ├── lblCapNumber        (sap.m.Label)
        ├── inpCapNumber        (sap.m.Input)
        ├── lblWorkOrderDate    (sap.m.Label)
        ├── dporderdate         (sap.m.DatePicker)   — Work Order Date
        ├── lblCompletionDt     (sap.m.Label)
        ├── dpOrderCompDate     (sap.m.DatePicker)   — Completion Date
        ├── lblFPAApprovalAtt   (sap.m.Label)
        └── vbFPAApprovalAtt    (sap.m.VBox)
            └── btnFPAApprovalAtt  (sap.m.Button)
```

---

### 3.3 Subsection: `subsectionProjAttDownload`

```
subsectionProjAttDownload
└── sfProjAttDownload  (sap.ui.layout.form.SimpleForm)
    ├── lblAttCAPEXLicenses       (sap.m.Label)
    ├── vbAttCAPEXLicenses        (sap.m.VBox)
    │   ├── lnkAttCAPEXLicenseDownload  (sap.m.Link)    — download CAPEX license
    │   └── btnAttCAPEXLicenseUpload    (sap.m.Button)  — upload CAPEX license
    │
    ├── lblAttSoftwareLicense     (sap.m.Label)
    ├── vbAttSoftwareLicense      (sap.m.VBox)
    │   └── btnAttSoftwareLicenseUpload (sap.m.Button)
    │
    ├── lblAttAccounting          (sap.m.Label)
    └── vbAttAccounting           (sap.m.VBox)
        ├── lnkAttAccountingDownload    (sap.m.Link)
        └── btnAttAccountingUpload      (sap.m.Button)
```

---

### 3.4 Subsection: `subsectionProjMultiCC`

```
subsectionProjMultiCC
└── ProjMultiCCode  (ObjectPageSubSection block)
    └── sfBudgetCCode  (sap.ui.layout.form.SimpleForm)
        └── tabMultiCC  (sap.m.Table)
            ├── tbTabMultiCCData  (sap.m.ToolbarSpacer / Toolbar)
            │   ├── tbSpacerCCode    (sap.m.ToolbarSpacer)
            │   └── btnAddCompCode   (sap.m.Button)  — add cost center row
            │
            ├── Columns:
            │   ├── colStatusICON       (sap.m.Column)
            │   ├── colCRegion          (sap.m.Column)
            │   ├── colCCompCode        (sap.m.Column)
            │   ├── colCCompCode_Text   (sap.m.Column)
            │   ├── colCostCenterCode   (sap.m.Column)
            │   ├── colCostCenterText   (sap.m.Column)
            │   └── colCDelete          (sap.m.Column)
            │
            └── listItemMultiCCode  (sap.m.ColumnListItem — row template)
                ├── oFormStatusIcon     (sap.ui.core.Icon)
                ├── txtCMultiCRegion    (sap.m.Text)
                ├── txtCMultiCCode      (sap.m.Text)
                ├── txtCMultiCCode_Text (sap.m.Text)
                ├── txtCMultiCostCenter (sap.m.Text)
                ├── txtCMultiCCenter_Text (sap.m.Text)
                └── btnDeleteCCode      (sap.m.Button)  — delete row
```

---

## 4. Section: `sectionApproval`

### 4.1 Subsection: `subsectionFPA`

```
subsectionFPA
└── oObjectPageSubSectionFPA  (ObjectPageSubSection)
    └── sfFPAAppr  (sap.ui.layout.form.SimpleForm)
        ├── lblExec_Category    (sap.m.Label)
        ├── oSelectExecCategory (sap.m.Select)
        ├── lblInit_Group       (sap.m.Label)
        ├── oSelectInitPrd_Grp  (sap.m.Select)
        ├── oSelectInit_Group   (sap.m.Select)  ← highlighted red (review required)
        ├── oLblFPAAppr         (sap.m.Label)
        └── oSwitchFPAAppr      (sap.m.Switch)  — FPA acknowledgement toggle
```

### 4.2 Subsection: `subsectionRTR`

```
subsectionRTR
└── oObjectPageSubSectionRTR  (ObjectPageSubSection)
    ├── sfRTRAppr  (sap.ui.layout.form.SimpleForm)
    │   ├── lblOrderType        (sap.m.Label)
    │   ├── oSelectOrderType    (sap.m.Select)
    │   ├── lblAssetClass       (sap.m.Label)
    │   ├── oSelectAssetClass   (sap.m.Select)
    │   ├── lblITLAssetClass    (sap.m.Label)
    │   ├── oSelectITLAssetClass (sap.m.Select)
    │   ├── oLblRTRAppr         (sap.m.Label)
    │   └── oSwitchRTRAppr      (sap.m.Switch)  — RTR acknowledgement toggle
    │
    └── sfRTROrderTbl  (sap.ui.layout.form.SimpleForm)
        ├── lblProjectID        (sap.m.Label)
        ├── txtProjectID        (sap.m.Text)
        └── tabOrderdetails     (sap.m.Table)
            ├── Columns:
            │   ├── colCompanyCode      (sap.m.Column)
            │   ├── colCostCenterCode1  (sap.m.Column)
            │   ├── colCProjCode        (sap.m.Column)
            │   ├── colCInternalCode    (sap.m.Column)
            │   └── colCErrorDesc       (sap.m.Column)
            └── listItems  (sap.m.ColumnListItem — row template)
                ├── txtCompCode         (sap.m.Text)
                ├── txtCostCenterCode   (sap.m.Text)
                ├── txtCProjCode        (sap.m.Text)
                ├── txtCInternalID      (sap.m.Text)
                └── txtCProjName        (sap.m.Text)
```

### 4.3 Subsection: `subsectionBPC`

```
subsectionBPC
└── oObjectPageSubSectionBPC  (ObjectPageSubSection)
    └── sfBPCAppr  (sap.ui.layout.form.SimpleForm)
        ├── oLblBPCAppr         (sap.m.Label)
        ├── oSwitchBPCAppr      (sap.m.Switch)  — BPC acknowledgement toggle
        ├── oLblProjCode        (sap.m.Label)   ← highlighted red
        ├── inpProjCode         (sap.m.Input)   ← highlighted red
        ├── oLblInternalOrder   (sap.m.Label)   ← highlighted red
        └── inpOnInternalNo     (sap.m.Input)   ← highlighted red
```

> **Red controls** (`oLblProjCode`, `inpProjCode`, `oLblInternalOrder`, `inpOnInternalNo`) are flagged in Neptune — verify binding and visibility rules before implementing.

---

## 5. Section: `sectionAttachments`

```
sectionAttachments
└── subsectionAttachments
    └── attachBlock1  (ObjectPageSubSection block)
        └── listAttachments  (sap.m.List)
            ├── tbListAttachments  (sap.m.Toolbar)
            │   ├── tbSpacerAttachments  (sap.m.ToolbarSpacer)
            │   └── btnAddAttachment     (sap.m.Button)  — opens diaAddAttachment
            └── listItemAttachment  (sap.m.CustomListItem — row template)
                [icon, file name, download link, delete button — per attachment entry]
```

---

## 6. Section: `sectionApprovals`

```
sectionApprovals
└── subsectionApprovals
    └── approvalBlock1  (ObjectPageSubSection block)
        └── oTableApprovers  (sap.m.Table)
            ├── Columns:
            │   ├── colDTableApproversSTEPS       (sap.m.Column)
            │   ├── colDTableApproversSTATUS_ICON (sap.m.Column)
            │   ├── coloDTableApproversSTEP_STATUS (sap.m.Column)
            │   ├── coloDTableApproversSTATUS_DATE (sap.m.Column)
            │   └── coloDTableApproversAPPRNAME   (sap.m.Column)
            └── colItemDTableApprovers  (sap.m.ColumnListItem — row template)
                ├── txtDTableApproversSTEPS       (sap.m.Text)
                ├── oDIconStatus                  (sap.ui.core.Icon)
                ├── txtoTableApproversSTEP_STATUS (sap.m.Text)
                ├── txtDTableApproversSTATUS_DATE (sap.m.Text)
                └── txtDTableApproversAPPROVERNAME (sap.m.Text)
```

---

## 7. Section: `sectionComments`

```
sectionComments
└── subsectionComments
    └── CommentsBlock  (ObjectPageSubSection block)
        └── oTextAreaPrevComments  (sap.m.TextArea)  — read-only previous comments display
```

---

## 8. Footer Toolbar: `footerPageCCreate`

```
footerPageCCreate  (sap.m.Bar — footer)
└── Right
    ├── butSave           (sap.m.Button)  — Save draft
    ├── butDelete         (sap.m.Button)  — Delete
    ├── butCreate         (sap.m.Button)  — Create → opens diaCreate
    ├── butChange         (sap.m.Button)  — Change → opens diaChange
    ├── butSubmitWthErr   (sap.m.Button)  — Submit with Error → opens diaSubmitWthErr
    ├── butSubmit         (sap.m.Button)  — Submit → opens diaSubmit
    ├── butReject         (sap.m.Button)  — Reject → opens diaReject
    ├── butClose          (sap.m.Button)  — Close/Withdraw → opens diaClose
    └── butResubmit       (sap.m.Button)  — Resubmit → opens diaReSubmit
```

> Button visibility is controlled by `ProgramState` / `modeloPageProj` flags. See `data_Structure_to_hold.md`.

---

## 9. Dialog Inventory: `oPageDialogs`

All dialogs are declared inside `oPageDialogs` container. Listed in Neptune tree order:

### 9.1 Multi-CC Dialog

```
btnAddMultiCCOK      (sap.m.Button)
btnAddMultiCCCancel  (sap.m.Button)
diaAddMultiCC  (sap.m.Dialog)
└── sfAddMultiCCodes  (sap.ui.layout.form.SimpleForm)
    ├── lblMultiCCode          (sap.m.Label)
    ├── oSelectMultiCCode      (sap.m.Select)
    ├── lblMultiCostCenter     (sap.m.Label)
    └── inpMultiCostCenter     (sap.m.Input — Value Help)
```

### 9.2 Previous Project Search Dialog

```
diaSearchPrevProj  (sap.m.SelectDialog or sap.m.Dialog)
└── listPrev_ProjCode  (sap.m.List / StandardListItem)
```

> **Status:** Spec not yet generated. See `diaSearchPrevProj.md` when available.

### 9.3 ReSubmit Dialog

```
butReSubmitCancel    (sap.m.Button)
butReSubmitSave      (sap.m.Button)
diaReSubmit  (sap.m.Dialog)
└── oTextAreaReSubmit  (sap.m.TextArea)
```

### 9.4 Agents Dialog

```
btnAgentsClose  (sap.m.Button)
diaAgents  (sap.m.Dialog or ResponsivePopover)
└── listAgents
    └── listItemAgent  (sap.m.StandardListItem)
```

### 9.5 Confirm Attachment Delete Dialog

```
btnAttDeleteNo   (sap.m.Button)
btnAttDeleteYes  (sap.m.Button)
diaConfirmAttDelete  (sap.m.Dialog)
└── txtConfirmAttDelete  (sap.m.Text)
```

### 9.6 Add Attachment Dialog

> **Full spec:** `diaAddAttachment.md`

```
diaAddAttachmentClose  (sap.m.Button)
diaAddAttachment  (sap.m.Dialog)
└── oFileUploader  (sap.ui.unified.FileUploader)
```

### 9.7 Employee Info Popover

> **Full spec:** `diaEmployeeInfo.md`

```
diaEmployeeInfo  (sap.m.ResponsivePopover)
└── oFormEmployeeInfo  (sap.ui.layout.form.SimpleForm)
    ├── lbloFormEmployeeInfoPERNR  / txtoFormEmployeeInfoPERNR
    ├── lbloFormEmployeeInfoENAME  / txtoFormEmployeeInfoENAME
    ├── lbloFormEmployeeInfoEMAIL  / txtoFormEmployeeInfoEMAIL
    ├── lbloFormEmployeeInfoCELL   / txtoFormEmployeeInfoCELL
    ├── lbloFormEmployeeInfoPHONE
    ├── oEmpPhone                  (sap.m.Link — tel: href)
    ├── lbloFormEmployeeInfoOTHER  / txtoFormEmployeeInfoOTHER
    [confirmed from page 7]
```

### 9.8 Reject Dialog

> **Full spec:** `diaReject_dialog_box.md`

```
butRejectOK       (sap.m.Button)
butRejectCancel   (sap.m.Button)
diaReject  (sap.m.Dialog)
└── oTextAreaReject  (sap.m.TextArea)
```

### 9.9 Timeout Dialog

```
butTimeoutClose  (sap.m.Button)
diaTimeout  (sap.m.Dialog)
└── oTextAreaTimeout  (sap.m.TextArea)
```

### 9.10 Create Dialog

> **Full spec:** `diaCreate_Dialog_Screen.md`

```
butCreateSave    (sap.m.Button)
butCreateCancel  (sap.m.Button)
diaCreate  (sap.m.Dialog)
└── oTextAreaCreate  (sap.m.TextArea)
```

### 9.11 Change Dialog

> **Full spec:** `Diachange.md`

```
butChgSave    (sap.m.Button)
butChgCancel  (sap.m.Button)
diaChange  (sap.m.Dialog)
└── oTextAreaChg  (sap.m.TextArea)
```

### 9.12 Submit Dialog

> **Full spec:** `diaSubmit.md`

```
butSubmitSave    (sap.m.Button)
butSubmitCancel  (sap.m.Button)
diaSubmit  (sap.m.Dialog)
└── oTextAreaSubmit  (sap.m.TextArea)
```

### 9.13 Submit With Error Dialog

> **Full spec:** `diaSubmitWthErr.md`

```
butSubWthErrSave    (sap.m.Button)
butSubWthErrCancel  (sap.m.Button)
diaSubmitWthErr  (sap.m.Dialog)
└── oTextAreaSubmitWthErr  (sap.m.TextArea)
```

### 9.14 INITIA_GRP Table (inline, not dialog)

```
oTableINITIA_GRP  (sap.m.Table)  — initiative group data table
```

### 9.15 Delete Draft Dialog

> **Full spec:** `diaDelDraft.md`

```
butDDraft        (sap.m.Button)
butDDraftCancel  (sap.m.Button)
diaDelDraft  (sap.m.Dialog)
└── oDDraftText  (sap.m.Text)  — "Confirm delete draft?"
```

### 9.16 Close/Withdraw Dialog

> **Full spec:** `diaClose.md`

```
butCloseSubmit   (sap.m.Button)
butCloseCancel   (sap.m.Button)
diaClose  (sap.m.Dialog)
└── oTextAreaCancel  (sap.m.TextArea)
```

### 9.17 Message Dialog

> **Full spec:** `diaMessage.md`

```
butMessageClose  (sap.m.Button)
diaMessage  (sap.m.Dialog)
└── sysMessage  (sap.m.VBox or container)
```

### 9.18 Search Dialogs (SelectDialog)

> **Full specs:** individual `dia*.md` files

```
diaSearchAuthBy        → listAuthBy_Person   (sap.m.StandardListItem)
diaSearchOnBehalf      → listOnBehalf_Person (sap.m.StandardListItem)
btnMessageClose        (sap.m.Button — standalone)
diaSearchCostCenter    → listCostCenter      (sap.m.StandardListItem)
diaGeography           → listItemGeography   (sap.m.StandardListItem)
diaBrand               → listItemBrand       (sap.m.StandardListItem)
diaLocation            → listItemLocation    (sap.m.StandardListItem)
```

---

## 10. Resources

```
Resources
├── Data  (model containers — see data_Structure_to_hold.md)
│   ├── AttachmentCategories  (GT_ATTACHMENT_CATEGORIES)
│   ├── AttachmentsRequired   (GT_ATTACHMENTS_REQUIRED)
│   ├── CompanyCodes          (GT_COMP_CODE)
│   ├── ProgramState          (WA_PROGRAM_STATE)
│   ├── cntr_formdetails      (GS_FORMDETAILS)
│   ├── cntr_stepdetails      (GS_STEPDETAIL)
│   └── UrlParameters         (WA_URL_PARAMETERS)
│
├── Init     (Init.txt   — app initialization, discoveryFiori setup)
├── Utility  (Utilities.txt — shared helper functions)
└── Form     (Form.txt   — validation functions)
```

---

## 11. Complete Spec File Index

| Spec File | Covers |
|-----------|--------|
| `subsectionRequester_SPEC.md` | Requester subsection — full control/binding/event spec |
| `subsectionProjdetail_SPEC.md` | Project Detail subsection |
| `Diachange.md` | `diaChange` dialog |
| `diaReject_dialog_box.md` | `diaReject` dialog |
| `diaCreate_Dialog_Screen.md` | `diaCreate` dialog |
| `diaSubmit.md` | `diaSubmit` dialog |
| `diaSubmitWthErr.md` | `diaSubmitWthErr` dialog |
| `diaClose.md` | `diaClose` dialog |
| `diaDelDraft.md` | `diaDelDraft` dialog |
| `diaAddAttachment.md` | `diaAddAttachment` dialog + file upload logic |
| `diaEmployeeInfo.md` | `diaEmployeeInfo` popover |
| `diaMessage.md` | `diaMessage` dialog |
| `diaSearchAuthBy.md` | Authorized-by person search |
| `diaSearchOnBehalf.md` | On-behalf-of person search |
| `diaSearchCostCenter.md` | Cost center search |
| `diaGeography.md` | Geography lookup |
| `diaBrand.md` | Brand lookup |
| `diaLocation.md` | Location lookup |
| `data_Structure_to_hold.md` | All 7 Data folder model structures + Fiori/RAP migration |
| *(pending)* `diaSearchPrevProj.md` | Previous project code search |
| *(pending)* `diaReSubmit.md` | Resubmit dialog |
| *(pending)* `diaTimeout.md` | Timeout dialog |
| *(pending)* `diaAddMultiCC.md` | Multi cost center add dialog |
| *(pending)* `diaConfirmAttDelete.md` | Attachment delete confirmation |
| *(pending)* `diaAgents.md` | Agents display dialog |

---

## 12. Fiori XML View — Top-Level Structure Template

```xml
<mvc:View
    controllerName="com.company.zdci.nad.projectrequest.controller.Main"
    xmlns="sap.m"
    xmlns:mvc="sap.ui.core.mvc"
    xmlns:uxap="sap.uxap"
    xmlns:form="sap.ui.layout.form"
    xmlns:unified="sap.ui.unified"
    xmlns:l="sap.ui.layout">

    <App id="oApp" busyIndicatorDelay="0">
        <pages>
            <Page id="oPageProj">

                <!-- Header -->
                <customHeader>
                    <Bar id="headerPageProj">
                        <contentLeft>
                            <Button id="btnNavBack" visible="false" />
                        </contentLeft>
                        <contentMiddle>
                            <Text id="titlePagePCreate" />
                        </contentMiddle>
                    </Bar>
                </customHeader>

                <!-- ObjectPageLayout -->
                <content>
                    <uxap:ObjectPageLayout id="oplForm">

                        <uxap:headerTitle>
                            <!-- ophProj with nav buttons -->
                        </uxap:headerTitle>

                        <uxap:headerContent>
                            <!-- hcAttributes: employee name, date, step, agent -->
                        </uxap:headerContent>

                        <uxap:sections>
                            <!-- sectionProj -->
                            <!-- sectionApproval -->
                            <!-- sectionAttachments -->
                            <!-- sectionApprovals -->
                            <!-- sectionComments -->
                        </uxap:sections>

                    </uxap:ObjectPageLayout>
                </content>

                <!-- Footer -->
                <footer>
                    <Bar id="footerPageCCreate">
                        <contentRight>
                            <!-- butSave, butDelete, butCreate, butChange,
                                 butSubmitWthErr, butSubmit, butReject,
                                 butClose, butResubmit -->
                        </contentRight>
                    </Bar>
                </footer>

            </Page>
        </pages>
    </App>

    <!-- All dialogs declared here (outside pages) -->
    <!-- diaChange, diaReject, diaCreate, diaSubmit, diaSubmitWthErr,
         diaClose, diaDelDraft, diaAddAttachment, diaEmployeeInfo,
         diaMessage, diaSearchAuthBy, diaSearchOnBehalf,
         diaSearchCostCenter, diaGeography, diaBrand, diaLocation,
         diaAddMultiCC, diaSearchPrevProj, diaReSubmit, diaTimeout,
         diaConfirmAttDelete, diaAgents -->

</mvc:View>
```
