# Diachange – Dialog Screen Specification

> **Source PDF:** `Diachange.pdf`  
> **Dialog Control ID:** `diaChange`  
> **Purpose:** Collects a change comment from the user and triggers the CHANGE AJAX call.

---

## 1. Dialog Root — `sap.m.Dialog`

| Property       | Value                        |
|----------------|------------------------------|
| Name           | `diaChange`                  |
| Model Source   | `WA_COMMENTS`                |
| Model Path     | *(empty)*                    |
| Ajax ID        | *(not add to View)*          |
| leftButton     | `butChgCancel`               |
| rightButton    | `butChgSave`                 |

### 1.1 Events

| Event       | Handler                    |
|-------------|----------------------------|
| `beforeOpen` | `oTextAreaChg.setValue();` |

---

## 2. Child Controls

### 2.1 `oTextAreaChg` — `sap.m.TextArea`

| Property              | Value                        |
|-----------------------|------------------------------|
| Name                  | `oTextAreaChg`               |
| Do not add to View    | `true` (only declare)        |
| placeholder           | `Add Comments`               |
| rows                  | `8`                          |
| value (binding)       | `{WA_COMMENTS>COMMENTS}`     |

> **Note:** The `value` binding path shown in the Neptune Cockpit is `{WA_COMMENTS-COMMENTS}` — map to model path `WA_COMMENTS>/COMMENTS` in Fiori.

---

### 2.2 `butChgCancel` — `sap.m.Button`

| Property | Value               |
|----------|---------------------|
| Name     | `butChgCancel`      |
| text     | `Cancel`            |

#### Press Event Handler

```javascript
// Anonymous Function
diaChange.close();
```

---

### 2.3 `butChgSave` — `sap.m.Button`

| Property | Value            |
|----------|------------------|
| Name     | `butChgSave`     |
| text     | `Submit Change`  |

#### Press Event Handler (full source)

```javascript
diaChange.close();
oApp.setBusy(true);

modeloPageProj.oData.COMMENTS = oTextAreaChg.getValue();
modeloPageProj.refresh();

if (rbITLYes.getSelected()) {
    modeloPageProj.oData.ITL = "Y";
    modeloPageProj.refresh();
} else {
    modeloPageProj.oData.ITL = "N";
    modeloPageProj.refresh();
}

clear_value();       // Clear Value based on condition
getOnlineChange();   // Triggers CHANGE AJAX call
```

---

## 3. Control Hierarchy Summary

```
diaChange  (sap.m.Dialog)
│  modelSource : WA_COMMENTS
│  leftButton  : butChgCancel
│  rightButton : butChgSave
│  beforeOpen  : oTextAreaChg.setValue();
│
├── oTextAreaChg  (sap.m.TextArea)
│     placeholder : "Add Comments"
│     rows        : 8
│     value       : {WA_COMMENTS>COMMENTS}
│
├── butChgCancel  (sap.m.Button)
│     text  : "Cancel"
│     press : diaChange.close();
│
└── butChgSave  (sap.m.Button)
      text  : "Submit Change"
      press : [see Section 2.3]
```

---

## 4. Model Dependencies

| Model             | Field      | Usage                                                   |
|-------------------|------------|---------------------------------------------------------|
| `WA_COMMENTS`     | `COMMENTS` | TextArea two-way value binding                          |
| `modeloPageProj`  | `COMMENTS` | Written on Save: `oData.COMMENTS = oTextAreaChg.getValue()` |
| `modeloPageProj`  | `ITL`      | Written on Save: `"Y"` or `"N"` based on `rbITLYes`    |

---

## 5. External Function Dependencies

| Function           | Location         | Purpose                                      |
|--------------------|------------------|----------------------------------------------|
| `clear_value()`    | `Utilities.txt`  | Clears conditional fields after change       |
| `getOnlineChange()` | AJAX layer      | Fires the `CHANGE` AJAX object               |

---

## 6. AJAX Reference

| AJAX Object | Trigger          | Notes                                  |
|-------------|------------------|----------------------------------------|
| `CHANGE`    | `getOnlineChange()` | Called after model update on Save   |

---

## 7. Fiori Migration — XML View Fragment

```xml
<!-- diaChange Dialog -->
<Dialog
    id="diaChange"
    beforeOpen=".onDiaChangeBeforeOpen"
    leftButton="butChgCancel"
    rightButton="butChgSave">

    <content>
        <TextArea
            id="oTextAreaChg"
            placeholder="Add Comments"
            rows="8"
            value="{WA_COMMENTS>/COMMENTS}" />
    </content>

    <buttons>
        <Button
            id="butChgCancel"
            text="Cancel"
            press=".onButChgCancelPress" />
        <Button
            id="butChgSave"
            text="Submit Change"
            press=".onButChgSavePress" />
    </buttons>
</Dialog>
```

---

## 8. Fiori Migration — Controller JavaScript

```javascript
onDiaChangeBeforeOpen: function () {
    this.byId("oTextAreaChg").setValue("");
},

onButChgCancelPress: function () {
    this.byId("diaChange").close();
},

onButChgSavePress: function () {
    var oView = this.getView();
    var oDialog = oView.byId("diaChange");
    var oTextArea = oView.byId("oTextAreaChg");

    oDialog.close();
    this.getView().getModel("oApp").setProperty("/busy", true);

    // Write comment back to main model
    var oModel = oView.getModel("modeloPageProj");
    oModel.setProperty("/COMMENTS", oTextArea.getValue());

    // Sync ITL flag
    var bITLYes = oView.byId("rbITLYes").getSelected();
    oModel.setProperty("/ITL", bITLYes ? "Y" : "N");

    // Clear conditional fields
    this._clearValue();

    // Trigger CHANGE AJAX
    this._getOnlineChange();
},
```

---

## 9. Migration Notes

1. **`beforeOpen` resets the TextArea** — `oTextAreaChg.setValue()` with no argument clears the field before the dialog opens. Replicate with `setValue("")` in Fiori.
2. **`WA_COMMENTS` model source** — In Neptune this is declared as the dialog's Model Source. In Fiori, bind via a named JSON model (`WA_COMMENTS`) set on the view or dialog.
3. **Double `modeloPageProj.refresh()`** — The Neptune source calls `refresh()` twice (once after COMMENTS, once after ITL). A single `refresh()` after both writes is sufficient in Fiori.
4. **`rbITLYes`** — This radio button lives outside `diaChange` (in `subsectionProjdetail`). The controller method must reference it by ID cross-control, or read the ITL flag from the model directly.
5. **Button placement** — Neptune uses `leftButton`/`rightButton` associations. In SAP Fiori XML, use the `<buttons>` aggregation instead; place Cancel first, Save second to preserve visual order.
