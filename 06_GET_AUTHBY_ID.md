# AJAX: GET_AUTHBY_ID
**App:** ZDCI_NAD_PROJECT_REQUEST
**AJAX Name:** SaveAsDraft
**AJAX ID:** GET_AUTHBY_ID
**Purpose:** Fetches the Authorised By user details and updates the form. On success, either goes back (if resubmit) or reloads full detail.

---

## Parameters

| Model | Field Type | Binding | Send | Receive | Description |
|-------|-----------|---------|------|---------|-------------|
| AttachmentCategories | ModelArray | GT_ATTACHMENT_CATEGORIES | ☐ | ☐ | |
| AttachmentsRequired | ModelArray | GT_ATTACHMENTS_REQUIRED | ☐ | ☐ | |
| cntr_formdetails | FlexBox | GS_FORMDETAILS | ☑ | ☑ | |
| cntr_stepdetails | FlexBox | GS_STEPDETAIL | ☑ | ☑ | |
| CompanyCodes | List | GT_COMP_CODE | ☐ | ☐ | |
| diaAddMultiCC | Dialog | GS_MULTIPLECCODE | ☐ | ☐ | |
| diaBrand | SelectDialog | GT_BRANDS | ☐ | ☐ | |
| diaChange | Dialog | WA_COMMENTS | ☐ | ☐ | |
| diaClose | Dialog | WA_COMMENTS | ☐ | ☐ | |
| diaConfirmAttDelete | Dialog | WA_CONFIRM_DELETE | ☐ | ☐ | |
| diaCreate | Dialog | WA_COMMENTS | ☐ | ☐ | |
| diaDelDraft | Dialog | GS_PROJ_FORM | ☐ | ☐ | |
| diaGeography | SelectDialog | GT_GEOGRAPHYLIST | ☐ | ☐ | |
| diaLocation | SelectDialog | GT_LOCATIONS | ☐ | ☐ | |
| diaReject | Dialog | WA_COMMENTS | ☐ | ☐ | |
| diaReSubmit | Dialog | WA_COMMENTS | ☐ | ☐ | |
| diaSearchAuthBy | SelectDialog | GT_AUTH_BY_LIST | ☐ | ☐ | Search Authorized By |
| diaSearchCostCenter | SelectDialog | GT_KOSTL | ☐ | ☐ | Search On CostCenter |
| diaSearchOnBehalf | SelectDialog | GT_AGENT_LIST | ☐ | ☐ | Search On Behalf Person |
| diaSearchPrevProj | SelectDialog | GT_PREV_PROJCODE | ☐ | ☐ | Search Previous Project Code |
| diaSubmit | Dialog | WA_COMMENTS | ☐ | ☐ | |
| diaSubmitWthErr | Dialog | WA_COMMENTS | ☐ | ☐ | Submit with error |
| listAgents | List | GT_ZCA_AGENT_LIST | ☐ | ☐ | |
| listAttachments | List | GT_ATTACHMENTS | ☑ | ☑ | |
| oFormEmployeeInfo | SimpleForm | GS_EMPLOYEE_INFO | ☐ | ☐ | |
| oPageProj | Page | GS_PROJ_FORM | ☑ | ☑ | |
| oSelectAssetClass | Select | GT_ASSET_CLASS | ☐ | ☐ | Asset-Class |
| oSelectCAPCURR | Select | GT_CURRENCY | ☐ | ☐ | |
| oSelectCompCode | Select | GT_COMP_CODE | ☐ | ☐ | Company Code |
| oSelectExecCategory | Select | GT_EXEC_CATEGORY | ☐ | ☐ | |
| oSelectInitPrd_Grp | Select | GT_INITI_GRP | ☐ | ☐ | |
| oSelectITLAssetClass | Select | GT_ITL_ASSET_CLASS | ☐ | ☐ | Asset-Class |
| oSelectITLCURR | Select | GT_CURRENCY | ☐ | ☐ | ITL Budget Currency |
| oSelectMultiCCode | Select | GT_ADD_COMP_CCODE | ☐ | ☐ | Select Multiple Company Code |
| oSelectOrderType | Select | GT_ORDER_TYPE | ☐ | ☐ | Order Type - Dropdown |
| oSelectPrevPCode | Select | GT_PREV_PROJCODE | ☐ | ☐ | Previous CAP Project Code? |
| oSelectProjType | Select | GT_REQUEST_TYPE | ☐ | ☐ | |
| oSelectUSState | Select | GT_USSTATE | ☐ | ☐ | Location/State |
| oTableApprovers | Table | GT_APPR | ☐ | ☐ | |
| oTableINITIA_GRP | Table | GT_INITI_GROUP | ☐ | ☐ | |
| ProgramState | FlexBox | WA_PROGRAM_STATE | ☐ | ☑ | |
| sectionAttachments | ObjectPageSection | GS_ATTACHMENT | ☑ | ☐ | |
| selProjectSubtype | Select | GT_PROJECT_SUBTYPE | ☐ | ☐ | |
| subsectionComments | ObjectPageSubSection | GS_PROJ_FORM | ☐ | ☐ | |
| tabMultiCC | Table | GT_ADDITIONAL_CCODE | ☑ | ☐ | Table for multiple CC |
| tabOrderdetails | Table | GT_ORDERDETAILS | ☐ | ☐ | |
| UrlParameters | FlexBox | WA_URL_PARAMETERS | ☐ | ☐ | |
| vlAttributesBlock1 | VerticalLayout | GS_FORMHEADER | ☐ | ☐ | |
| vlAttributesBlock2 | VerticalLayout | GS_FORMHEADER | ☐ | ☐ | |

---

## Send (INPUT to backend)
| Model | Binding | Notes |
|-------|---------|-------|
| cntr_formdetails | GS_FORMDETAILS | Form details with selected Authorised By user |
| cntr_stepdetails | GS_STEPDETAIL | Current workflow step |
| listAttachments | GT_ATTACHMENTS | Current attachment list |
| oPageProj | GS_PROJ_FORM | Current form page state |
| sectionAttachments | GS_ATTACHMENT | Attachment section data |
| tabMultiCC | GT_ADDITIONAL_CCODE | Multiple company codes table |

## Receive (OUTPUT from backend)
| Model | Binding | Notes |
|-------|---------|-------|
| cntr_formdetails | GS_FORMDETAILS | Updated with resolved Authorised By data |
| cntr_stepdetails | GS_STEPDETAIL | Updated step detail |
| listAttachments | GT_ATTACHMENTS | Updated attachment list |
| oPageProj | GS_PROJ_FORM | Updated form page including RESUBMIT flag |
| ProgramState | WA_PROGRAM_STATE | Error state check |

---

## ajaxSuccess
```js
setTimeout(function() {
    if (modeloPageProj.oData.RESUBMIT) {
        dc.goBack();
    } else {
        getOnlineGetDetail();
    }
}, 200);
sap.m.MessageToast.show(txtSaveDraft.getText());
```

## ajaxError
```js
getOnlineDequeueForm();
setTimeout(function() {
    oApp.setBusy(false);
    jQuery.sap.require("sap.m.MessageToast");
    sap.m.MessageToast.show(txtTranslateESaveAsDraft.getText());
    getOnlineoTreeTable();
    oApp.back();
}, 100);
dc.sendToParent({ event: "form-saved" });
dc.goBack();
```

---

## Notes
- Success: if `RESUBMIT` flag set on form → go straight back, else reload full detail via `getOnlineGetDetail()`
- Shows i18n toast `txtSaveDraft` on success regardless of RESUBMIT flag
- Error: dequeues form lock first, then shows error toast `txtTranslateESaveAsDraft`, refreshes tree table, navigates back
- Error path also sends `form-saved` to parent and calls `dc.goBack()` — form closes cleanly even on failure
- i18n keys used: `txtSaveDraft`, `txtTranslateESaveAsDraft`
- Helper functions: `getOnlineGetDetail()`, `getOnlineDequeueForm()`, `getOnlineoTreeTable()`
