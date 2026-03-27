# diaReject – Dialog Box Specification

> **Source PDF:** `diaReject_dialog_box.pdf`  
> **Dialog Control ID:** `diaReject`  
> **Purpose:** Collects a rejection comment from the user and triggers the REJECT AJAX call.

---

## 1. Dialog Root — `sap.m.Dialog`

| Property            | Value                                                        |
|---------------------|--------------------------------------------------------------|
| Name                | `diaReject`                                                  |
| Model Source        | `WA_COMMENTS`                                                |
| Model Path          | *(empty)*                                                    |
| Ajax ID             | *(not add to View)*                                          |
| leftButton          | `butRejectCancel`                                            |
| rightButton         | `butRejectOK`                                                |
| title               | `Reject Confirmation`                                        |
| contentHeight       | `200px`                                                      |
| contentWidth        | `500px`                                                      |
| draggable           | `true`                                                       |
| verticalScrolling   | `false`                                                      |

### 1.1 Events

| Event        | Handler                                                                 |
|--------------|-------------------------------------------------------------------------|
| `beforeOpen` | `oTextAreaReject.setValue();` // Clear old comments in dialog           |

---

## 2. Child Controls

### 2.1 `oTextAreaReject` — `sap.m.TextArea`

| Property           | Value                          |
|--------------------|--------------------------------|
| Name               | `oTextAreaReject`              |
| Do not add to View | `true` (only declare)          |
| placeholder        | `Add Comments`                 |
| rows               | `8`                            |
| value (binding)    | `{WA_COMMENTS>COMMENTS}`       |

> **Note:** Neptune Cockpit shows value binding as `{WA_COMMENTS-COMMENTS}`. Map to `WA_COMMENTS>/COMMENTS` in Fiori JSON model.

---

### 2.2 `butRejectCancel` — `sap.m.Button`

| Property | Value               |
|----------|---------------------|
| Name     | `butRejectCancel`   |
| text     | `Cancel`            |
| type     | `Default`           |

#### Press Event Handler

```javascript
// Anonymous Function
diaReject.close();
```

---

### 2.3 `butRejectOK` — `sap.m.Button`

| Property | Value     |
|----------|-----------|
| Name     | `butRejectOK` |
| text     | `Reject`  |
| tooltip  | `Reject`  |
| type     | *(empty)* |

#### Press Event Handler (full source)

```javascript
////////// ;
var sRejectText = modeldiaReject.oData.COMMENTS;
modeloPageProj.oData.COMMENTS = sRejectText.trim();
modeloPageProj.refresh();

if (sRejectText === '') {
    jQuery.sap.require("sap.m.MessageToast");
    sap.m.MessageToast.show("Comments are Required on Reject", {
        at: "center center"
    });
} else {
    oApp.setBusy(true);
    diaReject.close();
    getOnlineReject();   // Triggers REJECT AJAX call
}
```

---

## 3. Control Hierarchy Summary

```
diaReject  (sap.m.Dialog)
│  modelSource      : WA_COMMENTS
│  title            : "Reject Confirmation"
│  contentHeight    : 200px
│  contentWidth     : 500px
│  draggable        : true
│  verticalScrolling: false
│  leftButton       : butRejectCancel
│  rightButton      : butRejectOK
│  beforeOpen       : oTextAreaReject.setValue();
│
├── oTextAreaReject  (sap.m.TextArea)
│     placeholder : "Add Comments"
│     rows        : 8
│     value       : {WA_COMMENTS>COMMENTS}
│
├── butRejectCancel  (sap.m.Button)
│     text  : "Cancel"
│     type  : Default
│     press : diaReject.close();
│
└── butRejectOK  (sap.m.Button)
      text    : "Reject"
      tooltip : "Reject"
      press   : [see Section 2.3]
```

---

## 4. Model Dependencies

| Model              | Field      | Usage                                                                        |
|--------------------|------------|------------------------------------------------------------------------------|
| `WA_COMMENTS`      | `COMMENTS` | TextArea two-way value binding via `oTextAreaReject`                         |
| `modeldiaReject`   | `COMMENTS` | Read on OK press: `modeldiaReject.oData.COMMENTS`                            |
| `modeloPageProj`   | `COMMENTS` | Written on OK press: trimmed value from `modeldiaReject.oData.COMMENTS`      |

> **Note:** Two separate models are involved. `WA_COMMENTS` drives the TextArea binding; `modeldiaReject` is read directly on submit. Verify in the full app whether these are the same backing model or distinct — consolidate to one model in Fiori if possible.

---

## 5. Validation Logic

| Condition                    | Behaviour                                                              |
|------------------------------|------------------------------------------------------------------------|
| `sRejectText === ''`         | Show `MessageToast` at center: *"Comments are Required on Reject"*    |
| `sRejectText` has content    | Set app busy, close dialog, call `getOnlineReject()`                   |

> **Migration note:** In Fiori, replace `jQuery.sap.require("sap.m.MessageToast")` (deprecated) with a direct `sap.m.MessageToast.show()` call — the require is unnecessary in UI5 >= 1.58.

---

## 6. External Function Dependencies

| Function            | Location    | Purpose                           |
|---------------------|-------------|-----------------------------------|
| `getOnlineReject()` | AJAX layer  | Fires the `REJECT` AJAX object    |

---

## 7. AJAX Reference

| AJAX Object | Trigger             | Notes                                       |
|-------------|---------------------|---------------------------------------------|
| `REJECT`    | `getOnlineReject()` | Called only when comment field is non-empty |

---

## 8. Fiori Migration — XML View Fragment

```xml
<!-- diaReject Dialog -->
<Dialog
    id="diaReject"
    title="Reject Confirmation"
    contentHeight="200px"
    contentWidth="500px"
    draggable="true"
    verticalScrolling="false"
    beforeOpen=".onDiaRejectBeforeOpen"
    leftButton="butRejectCancel"
    rightButton="butRejectOK">

    <content>
        <TextArea
            id="oTextAreaReject"
            placeholder="Add Comments"
            rows="8"
            value="{WA_COMMENTS>/COMMENTS}" />
    </content>

    <buttons>
        <Button
            id="butRejectCancel"
            text="Cancel"
            type="Default"
            press=".onButRejectCancelPress" />
        <Button
            id="butRejectOK"
            text="Reject"
            tooltip="Reject"
            press=".onButRejectOKPress" />
    </buttons>
</Dialog>
```

---

## 9. Fiori Migration — Controller JavaScript

```javascript
onDiaRejectBeforeOpen: function () {
    this.byId("oTextAreaReject").setValue("");
},

onButRejectCancelPress: function () {
    this.byId("diaReject").close();
},

onButRejectOKPress: function () {
    var oView       = this.getView();
    var oModel      = oView.getModel("modeldiaReject");
    var oPageModel  = oView.getModel("modeloPageProj");

    var sRejectText = oModel.getProperty("/COMMENTS");
    oPageModel.setProperty("/COMMENTS", sRejectText ? sRejectText.trim() : "");

    if (!sRejectText || sRejectText.trim() === "") {
        sap.m.MessageToast.show("Comments are Required on Reject", {
            at: "center center"
        });
    } else {
        // Set app busy, close dialog, fire REJECT AJAX
        this.getView().getModel("oAppModel").setProperty("/busy", true);
        this.byId("diaReject").close();
        this._getOnlineReject();
    }
},
```

---

## 10. Differences vs. `diaChange`

| Aspect               | `diaChange`                          | `diaReject`                              |
|----------------------|--------------------------------------|------------------------------------------|
| Title                | *(none set)*                         | `"Reject Confirmation"`                  |
| contentHeight        | *(not set)*                          | `200px`                                  |
| contentWidth         | *(not set)*                          | `500px`                                  |
| draggable            | *(not set)*                          | `true`                                   |
| verticalScrolling    | *(not set)*                          | `false`                                  |
| TextArea control ID  | `oTextAreaChg`                       | `oTextAreaReject`                        |
| Save button text     | `"Submit Change"`                    | `"Reject"`                               |
| Validation           | None — always proceeds               | Guards empty comment with MessageToast   |
| ITL flag sync        | Yes — reads `rbITLYes`               | No                                       |
| AJAX trigger         | `getOnlineChange()`                  | `getOnlineReject()`                      |
| Source model on save | `oTextAreaChg.getValue()`            | `modeldiaReject.oData.COMMENTS`          |

---

## 11. Migration Notes

1. **`beforeOpen` clears the TextArea** — `oTextAreaReject.setValue()` with no argument clears prior input. Replicate with `setValue("")` in Fiori.
2. **Empty-comment guard** — Unlike `diaChange`, this dialog actively blocks submission when the comment is blank. The MessageToast must fire at `"center center"`.
3. **`jQuery.sap.require` deprecated** — Remove the `require` call; `sap.m.MessageToast` is available globally in modern UI5.
4. **Two model references** — Neptune reads from `modeldiaReject.oData.COMMENTS` (not from the TextArea directly). In Fiori, unify by reading from the bound model property `/COMMENTS` or directly from `oTextAreaReject.getValue()` — both should be equivalent if the binding is two-way.
5. **Button type** — `butRejectCancel` has `type: Default`; `butRejectOK` has no type set (renders as Default). Consider setting `butRejectOK` to `type="Reject"` (SAP semantic button type) in Fiori for consistent styling.
