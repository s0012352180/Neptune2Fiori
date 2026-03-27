# AJAX: BACK
**App:** ZDCI_NAD_PROJECT_REQUEST
**Purpose:** Navigates the user back to the list report, releasing any locks.

## Parameters
| Direction | Name | Type / Notes |
|-----------|------|--------------|
| INPUT | _(see screenshot in source doc)_ | Form identifier for lock release |
| OUTPUT | _(none recorded)_ | — |

> ⚠️ Parameter list captured as screenshot — fill in from Neptune designer.

## ajaxSuccess
```js
// No code recorded — add completion handler if needed
```

## ajaxError
```js
// No code recorded — add error handler if needed
```

## Notes
- Called via helper `getOnlineBack()`
- Used in CHANGE ajaxError to ensure clean navigation
- Typically dequeues form and triggers `dc.goBack()` or `oApp.back()`
