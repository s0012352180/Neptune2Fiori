# diaSubmit – Dialog Screen Specification

> **Source PDF:** `diaSubmit.pdf`  
> **Dialog Control ID:** `diaSubmit`  
> **Purpose:** Collects an optional comment from the user and triggers either the SUBMIT or NEW PROJECT AJAX call depending on the `PROJ_FLGRP_0003` flag.

---

## 1. Dialog Root — `sap.m.Dialog`

| Property          | Value               |
|-------------------|---------------------|
| Name              | `diaSubmit`         |
| Model Source      | `WA_COMMENTS`       |
| Model Path        | *(empty)*           |
| Ajax ID           | *(not add to View)* |
| leftButton        | `butSubmitCancel`   |
| rightButton       | `butSubmitSave`     |

### 1.1 Events

| Event        | Handler                          |
|--------------|----------------------------------|
| `beforeOpen` | `oTextAreaSubmit.setValue();`    |

> No title, contentHeight, contentWidth, or draggable set — unlike `diaReject` and `diaCreate`.

---

## 2. Child Controls

### 2.1 `oTextAreaSubmit` — `sap.m.TextArea`

| Property           | Value                    |
|--------------------|--------------------------|
| Name               | `oTextAreaSubmit`        |
| Do not add to View | `true` (only declare)    |
| placeholder        | `Add Comments`           |
| rows               | `8`                      |
| value (binding)    | `{WA_COMMENTS>COMMENTS}` |

> **Note:** Neptune Cockpit shows binding as `{WA_COMMENTS-COMMENTS}`. Map to `WA_COMMENTS>/COMMENTS` in Fiori JSON model.

---

### 2.2 `butSubmitCancel` — `sap.m.Button`

| Property | Value              |
|----------|--------------------|
| Name     | `butSubmitCancel`  |
| text     | `Cancel`           |
| type     | `Transparent`      |

#### Press Event Handler

```javascript
diaSubmit.close();
oApp.setBusy(false);
```

> **Note:** This is the only cancel button across all comment dialogs that explicitly calls `oApp.setBusy(false)`. The others simply close.

---

### 2.3 `butSubmitSave` — `sap.m.Button`

| Property | Value           |
|----------|-----------------|
| Name     | `butSubmitSave` |
| text     | `Submit`        |

#### Press Event Handler (full source)

```javascript
diaSubmit.close();
oApp.setBusy(true);

//var newComments = modeldiaSubmit.oData.COMMENTS;
modeloPageProj.oData.COMMENTS = oTextAreaSubmit.getValue();
modeloPageProj.refresh();

clear_value(); // Clear Value based on condition

if (!modeloPageProj.oData.PROJ_FLGRP_0003) {
    getOnlineSubmit();
} else if (modeloPageProj.oData.PROJ_FLGRP_0003) {
    getOnlineNewProject();
}
```

---

## 3. Control Hierarchy Summary

```
diaSubmit  (sap.m.Dialog)
│  modelSource  : WA_COMMENTS
│  leftButton   : butSubmitCancel
│  rightButton  : butSubmitSave
│  beforeOpen   : oTextAreaSubmit.setValue();
│
├── oTextAreaSubmit  (sap.m.TextArea)
│     placeholder : "Add Comments"
│     rows        : 8
│     value       : {WA_COMMENTS>COMMENTS}
│
├── butSubmitCancel  (sap.m.Button)
│     text  : "Cancel"
│     type  : Transparent
│     press : diaSubmit.close(); oApp.setBusy(false);
│
└── butSubmitSave  (sap.m.Button)
      text  : "Submit"
      press : [see Section 2.3]
```

---

## 4. Model Dependencies

| Model             | Field           | Usage                                                              |
|-------------------|-----------------|--------------------------------------------------------------------|
| `WA_COMMENTS`     | `COMMENTS`      | TextArea two-way value binding                                     |
| `modeloPageProj`  | `COMMENTS`      | Written on Save: `oTextAreaSubmit.getValue()`                      |
| `modeloPageProj`  | `PROJ_FLGRP_0003` | Read on Save: determines which AJAX call to fire                |

---

## 5. Branching Logic — AJAX Routing

| Condition                                  | AJAX Call              |
|--------------------------------------------|------------------------|
| `!modeloPageProj.oData.PROJ_FLGRP_0003`    | `getOnlineSubmit()`    |
| `modeloPageProj.oData.PROJ_FLGRP_0003`     | `getOnlineNewProject()`|

> **Migration note:** The two `if/else if` branches are logically exhaustive (both check the same boolean flag). This can be simplified to `if (...) { } else { }` in Fiori.

---

## 6. External Function Dependencies

| Function               | Location        | Purpose                                          |
|------------------------|-----------------|--------------------------------------------------|
| `clear_value()`        | `Utilities.txt` | Clears conditional fields after submit           |
| `getOnlineSubmit()`    | AJAX layer      | Fires the `SUBMIT` AJAX object                   |
| `getOnlineNewProject()`| AJAX layer      | Fires the `NEW PROJECT` AJAX object (RTR path)   |

---

## 7. AJAX Reference

| AJAX Object   | Trigger                 | Condition                          |
|---------------|-------------------------|------------------------------------|
| `SUBMIT`      | `getOnlineSubmit()`     | `PROJ_FLGRP_0003` is falsy         |
| `NEW PROJECT` | `getOnlineNewProject()` | `PROJ_FLGRP_0003` is truthy        |

---

## 8. Fiori Migration — XML View Fragment

```xml
<!-- diaSubmit Dialog -->
<Dialog
    id="diaSubmit"
    beforeOpen=".onDiaSubmitBeforeOpen"
    leftButton="butSubmitCancel"
    rightButton="butSubmitSave">

    <content>
        <TextArea
            id="oTextAreaSubmit"
            placeholder="Add Comments"
            rows="8"
            value="{WA_COMMENTS>/COMMENTS}" />
    </content>

    <buttons>
        <Button
            id="butSubmitCancel"
            text="Cancel"
            type="Transparent"
            press=".onButSubmitCancelPress" />
        <Button
            id="butSubmitSave"
            text="Submit"
            press=".onButSubmitSavePress" />
    </buttons>
</Dialog>
```

---

## 9. Fiori Migration — Controller JavaScript

```javascript
onDiaSubmitBeforeOpen: function () {
    this.byId("oTextAreaSubmit").setValue("");
},

onButSubmitCancelPress: function () {
    this.byId("diaSubmit").close();
    // Explicitly release busy state (unique to this dialog)
    this.getView().getModel("oAppModel").setProperty("/busy", false);
},

onButSubmitSavePress: function () {
    var oView      = this.getView();
    var oPageModel = oView.getModel("modeloPageProj");

    this.byId("diaSubmit").close();
    oView.getModel("oAppModel").setProperty("/busy", true);

    // Write comment from TextArea
    oPageModel.setProperty("/COMMENTS", this.byId("oTextAreaSubmit").getValue());

    // Clear conditional fields
    this._clearValue();

    // Route to correct AJAX call based on PROJ_FLGRP_0003
    if (!oPageModel.getProperty("/PROJ_FLGRP_0003")) {
        this._getOnlineSubmit();
    } else {
        this._getOnlineNewProject();
    }
},
```

---

## 10. Full Comparison Table — Comment Dialogs

| Aspect                  | `diaChange`                    | `diaReject`                    | `diaCreate`                              | `diaSubmit`                         |
|-------------------------|--------------------------------|--------------------------------|------------------------------------------|-------------------------------------|
| Title                   | *(none)*                       | `Reject Confirmation`          | `Confirmation to create new project request` | *(none)*                        |
| contentHeight           | *(not set)*                    | `200px`                        | `200px`                                  | *(not set)*                         |
| contentWidth            | *(not set)*                    | `500px`                        | `500px`                                  | *(not set)*                         |
| draggable               | *(not set)*                    | `true`                         | `true`                                   | *(not set)*                         |
| verticalScrolling       | *(not set)*                    | `false`                        | `false`                                  | *(not set)*                         |
| TextArea control ID     | `oTextAreaChg`                 | `oTextAreaReject`              | `oTextAreaCreate`                        | `oTextAreaSubmit`                   |
| Save button text        | `Submit Change`                | `Reject`                       | `Create`                                 | `Submit`                            |
| Save button type        | *(not set)*                    | *(not set)*                    | `Accept`                                 | *(not set)*                         |
| Cancel button type      | *(not set)*                    | `Default`                      | *(not set)*                              | `Transparent`                       |
| Cancel press extra      | *(none)*                       | *(none)*                       | *(none)*                                 | `oApp.setBusy(false)`               |
| Comment source on save  | `oTextAreaChg.getValue()`      | `modeldiaReject.oData.COMMENTS`| `oTextAreaCreate.getValue()`             | `oTextAreaSubmit.getValue()`        |
| Comment validation      | None                           | Guards empty — MessageToast    | None                                     | None                                |
| ITL flag sync           | Yes — reads `rbITLYes`         | No                             | Yes — reads `rbITLYes`                   | No                                  |
| AJAX routing            | Single: `getOnlineChange()`    | Single: `getOnlineReject()`    | Single: `getOnlineCreate()`              | Branched: Submit or NewProject      |
| Branch condition        | —                              | —                              | —                                        | `PROJ_FLGRP_0003`                   |

---

## 11. Migration Notes

1. **`beforeOpen` clears the TextArea** — `oTextAreaSubmit.setValue()` with no argument clears prior input. Replicate with `setValue("")` in Fiori.
2. **Cancel explicitly releases busy** — `oApp.setBusy(false)` is called on Cancel. This implies the app was set busy before the dialog was opened (likely during pre-submit validation). Ensure the Fiori controller mirrors this to prevent a stuck busy indicator.
3. **Dead code** — `//var newComments = modeldiaSubmit.oData.COMMENTS;` is commented out. Active path reads from `oTextAreaSubmit.getValue()` directly — same pattern as `diaCreate`.
4. **Branched AJAX** — This is the only comment dialog with two possible AJAX outcomes. The branch is driven solely by `PROJ_FLGRP_0003` (RTR approval group flag). Simplify the redundant `if/else if` to `if/else` in Fiori.
5. **No ITL sync** — Unlike `diaChange` and `diaCreate`, this dialog does not read or write the `ITL` flag. Do not add it.
6. **No title or size constraints** — `diaSubmit` renders with default dialog dimensions. If visual consistency with `diaReject`/`diaCreate` is desired, add `contentHeight="200px"` and `contentWidth="500px"` in Fiori — but keep it as-is for a strict 1:1 replica.
