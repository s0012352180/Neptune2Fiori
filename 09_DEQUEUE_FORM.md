# AJAX: DEQUEUE_FORM
**App:** ZDCI_NAD_PROJECT_REQUEST
**AJAX Name:** DequeueForm
**AJAX ID:** DEQUEUE_FORM
**Purpose:** Releases the form lock so other users can access it. Called as a utility from other AJAX error handlers.

---

## Parameters (Active only — Send or Receive checked)

| Model | Field Type | Binding | Send | Receive | Description |
|-------|-----------|---------|------|---------|-------------|
| cntr_formdetails | FlexBox | GS_FORMDETAILS | ☑ | ☐ | Form identifier for lock release |

---

## Send (INPUT to backend)
| Model | Binding |
|-------|---------|
| cntr_formdetails | GS_FORMDETAILS |

## Receive (OUTPUT from backend)
None.

---

## ajaxSuccess
```js
// No code — fire and forget
```

## ajaxError
```js
// No code — fire and forget
```

---

## Notes
- Pure utility AJAX — sends form ID to backend to release lock, no response handling needed
- Never called directly by user action — always triggered via `getOnlineDequeueForm()` helper
- Called from: `SAVE` (ajaxError), `CLOSE` (ajaxError), `REJECT` success (commented out)
- Must always be called when navigating away after an error to prevent form locks in backend
