# AJAX: GET_ONBEHALF_ID
**App:** ZDCI_NAD_PROJECT_REQUEST
**AJAX Name:** UpdateOnbehalf_ID
**AJAX ID:** GET_ONBEHALF_ID
**Purpose:** Updates the On Behalf Of user on the form and refreshes the Requester section.

---

## Parameters

| Model | Field Type | Binding | Send | Receive |
|-------|-----------|---------|------|---------|
| AttachmentCategories | ModelArray | GT_ATTACHMENT_CATEGORIES | ☐ | ☐ |
| AttachmentsRequired | ModelArray | GT_ATTACHMENTS_REQUIRED | ☐ | ☐ |
| cntr_formdetails | FlexBox | GS_FORMDETAILS | ☑ | ☑ |
| cntr_stepdetails | FlexBox | GS_STEPDETAIL | ☑ | ☑ |
| oFormEmployeeInfo | SimpleForm | GS_EMPLOYEE_INFO | ☐ | ☐ |
| oPageProj | Page | GS_PROJ_FORM | ☑ | ☑ |

---

## Send (INPUT to backend)
| Model | Binding | Notes |
|-------|---------|-------|
| cntr_formdetails | GS_FORMDETAILS | Form details including On Behalf user selection |
| cntr_stepdetails | GS_STEPDETAIL | Current workflow step |
| oPageProj | GS_PROJ_FORM | Current page/form state |

## Receive (OUTPUT from backend)
| Model | Binding | Notes |
|-------|---------|-------|
| cntr_formdetails | GS_FORMDETAILS | Updated with resolved On Behalf user data |
| cntr_stepdetails | GS_STEPDETAIL | Updated step detail |
| oPageProj | GS_PROJ_FORM | Updated form page data |

---

## ajaxSuccess
```js
setTimeout(function() {
    oplForm.scrollToSection(getControlName("subsectionRequester"));
}, 1000);
```

## ajaxError
```js
oApp.setBusy(false);
discoveryFiori.showMessage(
    txtConnectionFailure.getText(),
    sap.ui.core.ValueState.Error
);
```

---

## Notes
- Minimal success handler — only scrolls to Requester section after 1 second
- No `oApp.setBusy(false)` in success path — ensure busy is cleared before triggering this AJAX
- i18n key used: `txtConnectionFailure`
- Called when user selects an On Behalf Of person from `diaSearchOnBehalf` dialog
