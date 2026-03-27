# AJAX: RESUBMIT
**App:** ZDCI_NAD_PROJECT_REQUEST
**AJAX Name:** Resubmit
**AJAX ID:** RESUBMIT
**Purpose:** Resubmits a previously rejected or returned form back into the approval workflow.

---

## Parameters (Active only — Send or Receive checked)

| Model | Field Type | Binding | Send | Receive | Description |
|-------|-----------|---------|------|---------|-------------|
| cntr_formdetails | FlexBox | GS_FORMDETAILS | ☑ | ☐ | |
| cntr_stepdetails | FlexBox | GS_STEPDETAIL | ☑ | ☐ | |
| diaReSubmit | Dialog | WA_COMMENTS | ☑ | ☐ | Resubmit comments |
| listAttachments | List | GT_ATTACHMENTS | ☑ | ☐ | |
| oPageProj | Page | GS_PROJ_FORM | ☑ | ☐ | |
| tabMultiCC | Table | GT_ADDITIONAL_CCODE | ☑ | ☐ | |

---

## Send (INPUT to backend)
| Model | Binding |
|-------|---------|
| cntr_formdetails | GS_FORMDETAILS |
| cntr_stepdetails | GS_STEPDETAIL |
| diaReSubmit | WA_COMMENTS |
| listAttachments | GT_ATTACHMENTS |
| oPageProj | GS_PROJ_FORM |
| tabMultiCC | GT_ADDITIONAL_CCODE |

## Receive (OUTPUT from backend)
None.

---

## ajaxSuccess
```js
// Wait a bit for data read above, and then navigate
setTimeout(function() {
    oApp.setBusy(false);
    jQuery.sap.require("sap.m.MessageToast");
    sap.m.MessageToast.show(txtTranslateResubmit.getText());
}, 100);
// Launch event so that the caller can update their list
dirty.setDirty(false);
dc.sendToParent({ event: "form-saved" });
dc.goBack();
```

## ajaxError
```js
oApp.setBusy(false);
getOnlineBack();
// Launch event so that the caller can update their list
dc.sendToParent({ event: "form-saved" });
dc.goBack();
```

---

## Notes
- All parameters Send only — nothing returned from backend
- Success: 100ms delay then shows `txtTranslateResubmit` toast, clears dirty, notifies parent, goes back
- Error: calls `getOnlineBack()` to release lock, still notifies parent and goes back cleanly
- Same clean-exit pattern as CHANGE — error path navigates back without showing an error message
- i18n keys: `txtTranslateResubmit`
- Helpers: `getOnlineBack()`
