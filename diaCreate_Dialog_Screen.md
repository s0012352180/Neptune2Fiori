# diaCreate – Dialog Box Specification

> **Source PDF:** `diaCreate_Dialog_Screen.pdf`  
> **Dialog Control ID:** `diaCreate`  
> **Purpose:** Collects an optional comment from the user and triggers the CREATE AJAX call to create a new project request.

---

## 1. Dialog Root — `sap.m.Dialog`

| Property          | Value                                          |
|-------------------|------------------------------------------------|
| Name              | `diaCreate`                                    |
| Model Source      | `WA_COMMENTS`                                  |
| Model Path        | *(empty)*                                      |
| Ajax ID           | *(not add to View)*                            |
| leftButton        | `butCreateCancel`                              |
| rightButton       | `butCreateSave`                                |
| title             | `Confirmation to create new project request`   |
| contentHeight     | `200px`                                        |
| contentWidth      | `500px`                                        |
| draggable         | `true`                                         |
| verticalScrolling | `false`                                        |

### 1.1 Events

| Event        | Handler                                                                    |
|--------------|----------------------------------------------------------------------------|
| `beforeOpen` | `oTextAreaCreate.setValue();` // Clear old comments in dialog              |

---

## 2. Child Controls

### 2.1 `oTextAreaCreate` — `sap.m.TextArea`

| Property           | Value                        |
|--------------------|------------------------------|
| Name               | `oTextAreaCreate`            |
| Do not add to View | `true` (only declare)        |
| placeholder        | `Add Comments`               |
| cols               | `200`                        |
| rows               | `8`                          |
| value (binding)    | `{WA_COMMENTS>COMMENTS}`     |

> **Note:** Neptune Cockpit shows value binding as `{WA_COMMENTS-COMMENTS}`. Map to `WA_COMMENTS>/COMMENTS` in Fiori JSON model.

---

### 2.2 `butCreateSave` — `sap.m.Button`

| Property | Value              |
|----------|--------------------|
| Name     | `butCreateSave`    |
| text     | `Create`           |
| type     | `Accept`           |

#### Press Event Handler (full source)

```javascript
diaCreate.close();
oApp.setBusy(true);

//var newComments = modeldiaCreate.oData.COMMENTS;
modeloPageProj.oData.COMMENTS = oTextAreaCreate.getValue();
modeloPageProj.refresh();

// Update ITL flag from radio button
if (rbITLYes.getSelected()) {
    modeloPageProj.oData.ITL = "Y";
    modeloPageProj.refresh();
} else {
    modeloPageProj.oData.ITL = "N";
    modeloPageProj.refresh();
}

clear_value();       // Clear Value based on condition
getOnlineCreate();   // Triggers CREATE AJAX call
```

---

### 2.3 `butCreateCancel` — `sap.m.Button`

| Property | Value             |
|----------|-------------------|
| Name     | `butCreateCancel` |
| text     | `Cancel`          |

#### Press Event Handler

```javascript
// Close the dialog
diaCreate.close();
```

---

## 3. Control Hierarchy Summary

```
diaCreate  (sap.m.Dialog)
│  modelSource      : WA_COMMENTS
│  title            : "Confirmation to create new project request"
│  contentHeight    : 200px
│  contentWidth     : 500px
│  draggable        : true
│  verticalScrolling: false
│  leftButton       : butCreateCancel
│  rightButton      : butCreateSave
│  beforeOpen       : oTextAreaCreate.setValue();
│
├── oTextAreaCreate  (sap.m.TextArea)
│     placeholder : "Add Comments"
│     cols        : 200
│     rows        : 8
│     value       : {WA_COMMENTS>COMMENTS}
│
├── butCreateCancel  (sap.m.Button)
│     text  : "Cancel"
│     press : diaCreate.close();
│
└── butCreateSave  (sap.m.Button)
      text  : "Create"
      type  : Accept
      press : [see Section 2.2]
```

---

## 4. Model Dependencies

| Model             | Field      | Usage                                                                   |
|-------------------|------------|-------------------------------------------------------------------------|
| `WA_COMMENTS`     | `COMMENTS` | TextArea two-way value binding via `oTextAreaCreate`                    |
| `modeloPageProj`  | `COMMENTS` | Written on Save: `oTextAreaCreate.getValue()`                           |
| `modeloPageProj`  | `ITL`      | Written on Save: `"Y"` or `"N"` based on `rbITLYes.getSelected()`      |

> **Note:** Unlike `diaReject` which reads from `modeldiaReject.oData.COMMENTS`, this dialog reads directly from `oTextAreaCreate.getValue()`. The commented-out line `//var newComments = modeldiaCreate.oData.COMMENTS;` confirms this was an intentional change in Neptune source.

---

## 5. Validation Logic

No empty-comment guard exists on the Save button. The dialog closes and `getOnlineCreate()` fires unconditionally. Comment is optional for Create.

---

## 6. External Function Dependencies

| Function           | Location    | Purpose                            |
|--------------------|-------------|------------------------------------|
| `clear_value()`    | `Utilities.txt` | Clears conditional fields      |
| `getOnlineCreate()` | AJAX layer | Fires the `CREATE` AJAX object     |

---

## 7. AJAX Reference

| AJAX Object | Trigger              | Notes                                     |
|-------------|----------------------|-------------------------------------------|
| `CREATE`    | `getOnlineCreate()`  | Always called — no comment validation     |

---

## 8. Fiori Migration — XML View Fragment

```xml
<!-- diaCreate Dialog -->
<Dialog
    id="diaCreate"
    title="Confirmation to create new project request"
    contentHeight="200px"
    contentWidth="500px"
    draggable="true"
    verticalScrolling="false"
    beforeOpen=".onDiaCreateBeforeOpen"
    leftButton="butCreateCancel"
    rightButton="butCreateSave">

    <content>
        <TextArea
            id="oTextAreaCreate"
            placeholder="Add Comments"
            cols="200"
            rows="8"
            value="{WA_COMMENTS>/COMMENTS}" />
    </content>

    <buttons>
        <Button
            id="butCreateCancel"
            text="Cancel"
            press=".onButCreateCancelPress" />
        <Button
            id="butCreateSave"
            text="Create"
            type="Accept"
            press=".onButCreateSavePress" />
    </buttons>
</Dialog>
```

---

## 9. Fiori Migration — Controller JavaScript

```javascript
onDiaCreateBeforeOpen: function () {
    this.byId("oTextAreaCreate").setValue("");
},

onButCreateCancelPress: function () {
    this.byId("diaCreate").close();
},

onButCreateSavePress: function () {
    var oView      = this.getView();
    var oPageModel = oView.getModel("modeloPageProj");

    this.byId("diaCreate").close();
    // Set app busy
    oView.getModel("oAppModel").setProperty("/busy", true);

    // Write comment directly from TextArea
    oPageModel.setProperty("/COMMENTS", this.byId("oTextAreaCreate").getValue());

    // Sync ITL flag from radio button
    var bITLYes = oView.byId("rbITLYes").getSelected();
    oPageModel.setProperty("/ITL", bITLYes ? "Y" : "N");

    // Clear conditional fields
    this._clearValue();

    // Trigger CREATE AJAX
    this._getOnlineCreate();
},
```

---

## 10. Comparison Table — Comment Dialogs

| Aspect                | `diaChange`                   | `diaReject`                    | `diaCreate`                              |
|-----------------------|-------------------------------|--------------------------------|------------------------------------------|
| Title                 | *(none)*                      | `Reject Confirmation`          | `Confirmation to create new project request` |
| contentHeight         | *(not set)*                   | `200px`                        | `200px`                                  |
| contentWidth          | *(not set)*                   | `500px`                        | `500px`                                  |
| draggable             | *(not set)*                   | `true`                         | `true`                                   |
| verticalScrolling     | *(not set)*                   | `false`                        | `false`                                  |
| TextArea control ID   | `oTextAreaChg`                | `oTextAreaReject`              | `oTextAreaCreate`                        |
| Save button text      | `Submit Change`               | `Reject`                       | `Create`                                 |
| Save button type      | *(not set)*                   | *(not set)*                    | `Accept`                                 |
| Comment source on save| `oTextAreaChg.getValue()`     | `modeldiaReject.oData.COMMENTS`| `oTextAreaCreate.getValue()`             |
| Comment validation    | None                          | Guards empty — MessageToast    | None — always proceeds                   |
| ITL flag sync         | Yes — reads `rbITLYes`        | No                             | Yes — reads `rbITLYes`                   |
| AJAX trigger          | `getOnlineChange()`           | `getOnlineReject()`            | `getOnlineCreate()`                      |

---

## 11. Migration Notes

1. **`beforeOpen` clears the TextArea** — `oTextAreaCreate.setValue()` with no argument clears prior input. Replicate with `setValue("")` in Fiori.
2. **Comment is optional** — No validation guard on the Save button; `getOnlineCreate()` fires even with an empty comment field.
3. **Dead code in source** — The line `//var newComments = modeldiaCreate.oData.COMMENTS;` is commented out. The active path reads directly from `oTextAreaCreate.getValue()`. Do not rely on `modeldiaCreate` model for comments in this dialog.
4. **`type="Accept"`** — `butCreateSave` uses SAP semantic button type `Accept` (renders green). Preserve this in Fiori for visual consistency.
5. **ITL sync** — Same pattern as `diaChange`; `rbITLYes` lives outside the dialog in `subsectionProjdetail`. Reference by view ID in Fiori controller.
6. **Double `refresh()`** — Neptune calls `modeloPageProj.refresh()` after each of COMMENTS and ITL. Consolidate to a single `refresh()` after both writes in Fiori.
