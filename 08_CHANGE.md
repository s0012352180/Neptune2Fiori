# AJAX: CHANGE
**App:** ZDCI_NAD_PROJECT_REQUEST
**Purpose:** Saves a change/update to an existing form (edit mode).

## Parameters
| Direction | Name | Type / Notes |
|-----------|------|--------------|
| INPUT | _(see screenshot in source doc)_ | Updated form data |
| OUTPUT | _(see screenshot in source doc)_ | Change confirmation |

> ⚠️ Full parameter list captured as screenshots — fill in from Neptune designer.

## ajaxSuccess
```js
setTimeout(function() {
    oApp.setBusy(false);
    jQuery.sap.require("sap.m.MessageToast");
    sap.m.MessageToast.show(txtTranslateChange.getText());
}, 100);
dirty.setDirty(false);
dc.sendToParent({ event: "form-saved" });
dc.goBack();
```

## ajaxError
```js
oApp.setBusy(false);
getOnlineBack();
dc.sendToParent({ event: "form-saved" });
dc.goBack();
```

## Notes
- Success: shows i18n toast `txtTranslateChange`, clears dirty, notifies parent, goes back
- Error: calls `getOnlineBack()` helper before navigating — still sends `form-saved` to parent
- Note: error path also goes back (no error message shown — consider adding one)
