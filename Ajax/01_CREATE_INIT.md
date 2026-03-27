# AJAX: CREATE_INIT
**App:** ZDCI_NAD_PROJECT_REQUEST
**Purpose:** Called on form creation — initialises all variables and fetches defaults from backend class.

## Parameters
| Direction | Name | Type / Notes |
|-----------|------|--------------|
| INPUT | _(see screenshot in source doc)_ | Params passed to backend on init |
| OUTPUT | _(see screenshot in source doc)_ | Default values returned to form |

> ⚠️ Parameters captured as screenshots in source — fill in from Neptune designer.

## ajaxSuccess
```js
setUI();
oApp.to(modeloPageProj);
oApp.setBusy(false);
setprocessingregion();
setTimeout(function() {
    oplForm.scrollToSection(getControlName("subsectionRequester"));
}, 1000);
```

## ajaxError
```js
oApp.setBusy(false);
```

## Notes
- Navigates to the project form page after init
- Scrolls to Requester section after 1 second
- Calls `setUI()` and `setprocessingregion()` helper functions
