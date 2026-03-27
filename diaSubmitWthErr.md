# diaSubmitWthErr – Dialog Screen Specification

> **Source PDF:** `diaSubmitWthErr.pdf`  
> **Dialog Control ID:** `diaSubmitWthErr`  
> **Description:** `Submit with error`  
> **Purpose:** Collects a comment when the user submits a form that has validation errors. Triggers `getOnlineNewIntOrderWErr()` only when `PROJ_FLGRP_0003` is truthy.

---

## 1. Dialog Root — `sap.m.Dialog`

| Property          | Value                  |
|-------------------|------------------------|
| Name              | `diaSubmitWthErr`      |
| Description       | `Submit with error`    |
| Model Source      | `WA_COMMENTS`          |
| Model Path        | *(empty)*              |
| Ajax ID           | *(not add to View)*    |
| leftButton        | `butSubWthErrCancel`   |
| rightButton       | `butSubWthErrSave`     |

### 1.1 Events

| Event        | Handler                                  |
|--------------|------------------------------------------|
| `beforeOpen` | `oTextAreaSubmitWthErr.setValue();`      |

> No title, contentHeight, contentWidth, or draggable set — same minimal footprint as `diaSubmit`.

---

## 2. Child Controls

### 2.1 `oTextAreaSubmitWthErr` — `sap.m.TextArea`

| Property           | Value                    |
|--------------------|--------------------------|
| Name               | `oTextAreaSubmitWthErr`  |
| Do not add to View | `true` (only declare)    |
| placeholder        | *(not explicitly set — inherits default)* |
| value (binding)    | `{WA_COMMENTS>COMMENTS}` |

> **Note:** Neptune tree shows the TextArea as a child of `diaSubmitWthErr`. No explicit `rows` or `placeholder` value was visible in the PDF properties panel. Treat as equivalent to other comment dialogs (rows=8, placeholder "Add Comments") unless source XML confirms otherwise.

---

### 2.2 `butSubWthErrSave` — `sap.m.Button`

| Property | Value              |
|----------|--------------------|
| Name     | `butSubWthErrSave` |
| text     | `Submit`           |

#### Press Event Handler (full source)

```javascript
diaSubmitWthErr.close();
oApp.setBusy(true);

modeloPageProj.oData.COMMENTS = oTextAreaSubmitWthErr.getValue();
modeloPageProj.refresh();

clear_value(); // Clear Value based on condition

if (modeloPageProj.oData.PROJ_FLGRP_0003) {
    getOnlineNewIntOrderWErr();
}
```

> **Critical note:** The AJAX call `getOnlineNewIntOrderWErr()` fires **only if `PROJ_FLGRP_0003` is truthy**. There is **no else branch** — if `PROJ_FLGRP_0003` is false, the dialog closes and the app is set busy but no AJAX call fires. This appears intentional: this dialog is only expected to be opened in the RTR/PROJ_FLGRP_0003 flow.

---

### 2.3 `butSubWthErrCancel` — `sap.m.Button`

| Property | Value               |
|----------|---------------------|
| Name     | `butSubWthErrCancel`|
| text     | `Cancel`            |

#### Press Event Handler

```javascript
diaSubmitWthErr.close();
oApp.setBusy(false);
```

> Same pattern as `butSubmitCancel` in `diaSubmit` — explicitly releases the busy state on Cancel.

---

## 3. Control Hierarchy Summary

```
diaSubmitWthErr  (sap.m.Dialog)
│  description      : "Submit with error"
│  modelSource      : WA_COMMENTS
│  leftButton       : butSubWthErrCancel
│  rightButton      : butSubWthErrSave
│  beforeOpen       : oTextAreaSubmitWthErr.setValue();
│
├── oTextAreaSubmitWthErr  (sap.m.TextArea)
│     value : {WA_COMMENTS>COMMENTS}
│
├── butSubWthErrCancel  (sap.m.Button)
│     text  : "Cancel"
│     press : diaSubmitWthErr.close(); oApp.setBusy(false);
│
└── butSubWthErrSave  (sap.m.Button)
      text  : "Submit"
      press : [see Section 2.2]
```

---

## 4. Model Dependencies

| Model             | Field             | Usage                                                      |
|-------------------|-------------------|------------------------------------------------------------|
| `WA_COMMENTS`     | `COMMENTS`        | TextArea two-way value binding                             |
| `modeloPageProj`  | `COMMENTS`        | Written on Save: `oTextAreaSubmitWthErr.getValue()`        |
| `modeloPageProj`  | `PROJ_FLGRP_0003` | Read on Save: gates `getOnlineNewIntOrderWErr()` call      |

---

## 5. Branching Logic — AJAX Routing

| Condition                                   | AJAX Call                    |
|---------------------------------------------|------------------------------|
| `modeloPageProj.oData.PROJ_FLGRP_0003` true | `getOnlineNewIntOrderWErr()` |
| `modeloPageProj.oData.PROJ_FLGRP_0003` false | *(no AJAX — dead path)*     |

> **vs. `diaSubmit`:** `diaSubmit` has a two-branch `if/else if` covering both true and false states of `PROJ_FLGRP_0003`. `diaSubmitWthErr` has only the `if (true)` branch — the false path is unhandled. This dialog is exclusively for the error-override flow within the RTR group.

---

## 6. External Function Dependencies

| Function                   | Location    | Purpose                                              |
|----------------------------|-------------|------------------------------------------------------|
| `clear_value()`            | `Utilities.txt` | Clears conditional fields                        |
| `getOnlineNewIntOrderWErr()` | AJAX layer | Fires SUBMIT WITH ERROR AJAX (RTR/PROJ_FLGRP_0003 path) |

---

## 7. AJAX Reference

| AJAX Object            | Trigger                      | Condition                         |
|------------------------|------------------------------|-----------------------------------|
| `NEW_INT_ORDER_W_ERR`  | `getOnlineNewIntOrderWErr()` | `PROJ_FLGRP_0003` is truthy only  |

---

## 8. Fiori Migration — XML View Fragment

```xml
<!-- diaSubmitWthErr Dialog -->
<Dialog
    id="diaSubmitWthErr"
    beforeOpen=".onDiaSubmitWthErrBeforeOpen"
    leftButton="butSubWthErrCancel"
    rightButton="butSubWthErrSave">

    <content>
        <TextArea
            id="oTextAreaSubmitWthErr"
            placeholder="Add Comments"
            rows="8"
            value="{WA_COMMENTS>/COMMENTS}" />
    </content>

    <buttons>
        <Button
            id="butSubWthErrCancel"
            text="Cancel"
            press=".onButSubWthErrCancelPress" />
        <Button
            id="butSubWthErrSave"
            text="Submit"
            press=".onButSubWthErrSavePress" />
    </buttons>
</Dialog>
```

---

## 9. Fiori Migration — Controller JavaScript

```javascript
onDiaSubmitWthErrBeforeOpen: function () {
    this.byId("oTextAreaSubmitWthErr").setValue("");
},

onButSubWthErrCancelPress: function () {
    this.byId("diaSubmitWthErr").close();
    // Explicitly release busy state
    this.getView().getModel("oAppModel").setProperty("/busy", false);
},

onButSubWthErrSavePress: function () {
    var oView      = this.getView();
    var oPageModel = oView.getModel("modeloPageProj");

    this.byId("diaSubmitWthErr").close();
    oView.getModel("oAppModel").setProperty("/busy", true);

    // Write comment from TextArea
    oPageModel.setProperty("/COMMENTS", this.byId("oTextAreaSubmitWthErr").getValue());

    // Clear conditional fields
    this._clearValue();

    // Fire AJAX only for PROJ_FLGRP_0003 path
    if (oPageModel.getProperty("/PROJ_FLGRP_0003")) {
        this._getOnlineNewIntOrderWErr();
    }
    // Note: no else branch — this dialog is exclusive to the RTR error-override flow
},
```

---

## 10. Full Comparison Table — All Comment Dialogs

| Aspect                  | `diaChange`                    | `diaReject`                    | `diaCreate`                              | `diaSubmit`                         | `diaSubmitWthErr`                    |
|-------------------------|--------------------------------|--------------------------------|------------------------------------------|-------------------------------------|--------------------------------------|
| Description             | *(none)*                       | *(none)*                       | *(none)*                                 | *(none)*                            | `Submit with error`                  |
| Title                   | *(none)*                       | `Reject Confirmation`          | `Confirmation to create new project request` | *(none)*                        | *(none)*                             |
| contentHeight           | *(not set)*                    | `200px`                        | `200px`                                  | *(not set)*                         | *(not set)*                          |
| contentWidth            | *(not set)*                    | `500px`                        | `500px`                                  | *(not set)*                         | *(not set)*                          |
| draggable               | *(not set)*                    | `true`                         | `true`                                   | *(not set)*                         | *(not set)*                          |
| TextArea control ID     | `oTextAreaChg`                 | `oTextAreaReject`              | `oTextAreaCreate`                        | `oTextAreaSubmit`                   | `oTextAreaSubmitWthErr`              |
| Save button text        | `Submit Change`                | `Reject`                       | `Create`                                 | `Submit`                            | `Submit`                             |
| Save button type        | *(not set)*                    | *(not set)*                    | `Accept`                                 | *(not set)*                         | *(not set)*                          |
| Cancel button type      | *(not set)*                    | `Default`                      | *(not set)*                              | `Transparent`                       | *(not set)*                          |
| Cancel press extra      | *(none)*                       | *(none)*                       | *(none)*                                 | `oApp.setBusy(false)`               | `oApp.setBusy(false)`                |
| Comment validation      | None                           | Guards empty — MessageToast    | None                                     | None                                | None                                 |
| ITL flag sync           | Yes                            | No                             | Yes                                      | No                                  | No                                   |
| AJAX routing            | Single: `getOnlineChange()`    | Single: `getOnlineReject()`    | Single: `getOnlineCreate()`              | Branched: Submit or NewProject      | Conditional: NewIntOrderWErr only    |
| Branch condition        | —                              | —                              | —                                        | `PROJ_FLGRP_0003` (both branches)   | `PROJ_FLGRP_0003` (true only)        |
| Unhandled false path    | No                             | No                             | No                                       | No                                  | **Yes — no else branch**             |

---

## 11. Migration Notes

1. **`beforeOpen` clears the TextArea** — `oTextAreaSubmitWthErr.setValue()` with no argument. Replicate with `setValue("")` in Fiori.
2. **No else branch on AJAX** — Unlike `diaSubmit` which handles both true/false states of `PROJ_FLGRP_0003`, this dialog only fires AJAX when the flag is true. The false path is a silent no-op after setting the app busy. Preserve this exactly — do not add an else branch.
3. **Cancel releases busy** — Same pattern as `diaSubmit`'s cancel. The app must have been set busy before this dialog opened (likely during pre-submit validation that detected errors). Fiori controller must mirror `setBusy(false)` on cancel.
4. **Exclusive to RTR error-override flow** — This dialog is only expected to open when `PROJ_FLGRP_0003` is true AND the form has validation errors. It should not be opened in any other context.
5. **TextArea properties not fully visible** — The PDF properties panel for `oTextAreaSubmitWthErr` was partially cut off. Assume `rows=8` and `placeholder="Add Comments"` consistent with all other comment dialogs unless the source XML shows otherwise.
6. **Button control IDs differ from `diaSubmit`** — `diaSubmit` uses `butSubmitCancel`/`butSubmitSave`; this dialog uses `butSubWthErrCancel`/`butSubWthErrSave`. Ensure no ID collision in the Fiori view.
