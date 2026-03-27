# AJAX: BACK
**App:** ZDCI_NAD_PROJECT_REQUEST
**AJAX Name:** BACK
**AJAX ID:** BACK
**Purpose:** Navigates back to the list report, releasing any form locks in the backend.

---

## Parameters (Active only — Send or Receive checked)

| Model | Field Type | Binding | Send | Receive | Description |
|-------|-----------|---------|------|---------|-------------|
| cntr_formdetails | FlexBox | GS_FORMDETAILS | ☑ | ☐ | Form identifier for lock release |
| cntr_stepdetails | FlexBox | GS_STEPDETAIL | ☑ | ☐ | Current step detail |

---

## Send (INPUT to backend)
| Model | Binding |
|-------|---------|
| cntr_formdetails | GS_FORMDETAILS |
| cntr_stepdetails | GS_STEPDETAIL |

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
- Utility AJAX — sends form and step detail to backend to release lock, no response handling needed
- Never called directly by user action — always triggered via `getOnlineBack()` helper
- Called from: `CHANGE` (ajaxError)
- Sends one extra parameter vs DEQUEUE_FORM — includes `GS_STEPDETAIL` alongside `GS_FORMDETAILS`
