# AJAX: CLOSE
**App:** ZDCI_NAD_PROJECT_REQUEST
**Purpose:** Withdraws / closes the form and returns to the list report.

## Parameters
| Direction | Name | Type / Notes |
|-----------|------|--------------|
| INPUT | _(see screenshots — 5 images in source doc)_ | Form identifier + close reason |
| OUTPUT | _(see screenshots)_ | Confirmation |

> ⚠️ Full parameter list captured as 5 screenshots — fill in from Neptune designer.

## ajaxSuccess
```js
setTimeout(function() {
    oApp.setBusy(false);
    jQuery.sap.require("sap.m.MessageToast");
    sap.m.MessageToast.show(txtTranslateWithdrawn.getText());
}, 100);
dc.sendToParent({ event: "form-saved" });
dc.goBack();
```

## ajaxError
```js
oApp.setBusy(false);
getOnlineDequeueForm();
dc.sendToParent({ event: "form-saved" });
dc.goBack();
```

## Notes
- Success: shows i18n toast `txtTranslateWithdrawn`, notifies parent, goes back
- Error: dequeues form lock, still notifies parent and goes back (no error message — consider adding one)
- i18n key needed: `txtTranslateWithdrawn`
