# diaClose – Dialog Screen Specification

> **Source PDF:** `diaClose.pdf`  
> **Dialog Control ID:** `diaClose`  
> **Purpose:** Collects a withdrawal/close comment from the user and triggers the CLOSE AJAX call to withdraw the project request.

---

## 1. Dialog Root — `sap.m.Dialog`

| Property          | Value               |
|-------------------|---------------------|
| Name              | `diaClose`          |
| Description       | *(none)*            |
| Model Source      | `WA_COMMENTS`       |
| Model Path        | *(empty)*           |
| Ajax ID           | *(not add to View)* |
| leftButton        | `butCloseCancel`    |
| rightButton       | `butCloseSubmit`    |
| title             | `Confirm Withdraw`  |
| contentHeight     | `200px`             |
| contentWidth      | `500px`             |
| draggable         | `true`              |
| verticalScrolling | `false`             |

### 1.1 Events

| Event        | Handler                   |
|--------------|---------------------------|
| `beforeOpen` | *(not set)*               |

> **Note:** No `beforeOpen` handler is set on this dialog. Unlike all other comment dialogs, `oTextAreaCancel` is NOT cleared before opening. This may be intentional (preserving prior withdrawal text) or an oversight in the Neptune source.

---

## 2. Child Controls

### 2.1 `oTextAreaCancel` — `sap.m.TextArea`

| Property           | Value                    |
|--------------------|--------------------------|
| Name               | `oTextAreaCancel`        |
| Do not add to View | *(not confirmed)*        |
| value (binding)    | *(inherits WA_COMMENTS)* |

> **Note:** The TextArea child is visible in the Neptune tree as `oTextAreaCancel` under `diaClose`. Placeholder and rows properties were not captured in the PDF. Apply `placeholder="Add Comments"` and `rows=8` consistent with other comment dialogs unless source XML confirms otherwise.

---

### 2.2 `butCloseCancel` — `sap.m.Button`

| Property | Value            |
|----------|------------------|
| Name     | `butCloseCancel` |
| text     | `Cancel`         |

#### Press Event Handler

```javascript
diaClose.close();
oApp.setBusy(false);
```

> **Note:** Like `diaSubmit` and `diaSubmitWthErr`, this cancel explicitly calls `oApp.setBusy(false)`, confirming the app was set busy before the dialog opened.

---

### 2.3 `butCloseSubmit` — `sap.m.Button`

| Property | Value             |
|----------|-------------------|
| Name     | `butCloseSubmit`  |
| text     | `Withdraw`        |

#### Press Event Handler (full source)

```javascript
oApp.setBusy(true);
diaClose.close();

//var newComments = modeldiaClose.oData.COMMENTS;
modeloPageProj.oData.COMMENTS = oTextAreaCancel.getValue();
modeloPageProj.refresh();

getOnlineClose();
```

> **Critical note:** The PDF shows two separate code blocks on page 3. The first block belongs to `butCloseCancel`:
> ```javascript
> diaClose.close();
> oApp.setBusy(false);
> ```
> The second block belongs to `butCloseSubmit`:
> ```javascript
> oApp.setBusy(true);
> diaClose.close();
> //var newComments = modeldiaClose.oData.COMMENTS;
> modeloPageProj.oData.COMMENTS = oTextAreaCancel.getValue();
> modeloPageProj.refresh();
> getOnlineClose();
> ```
> Note the **reversed order** vs. other Save handlers: `oApp.setBusy(true)` is called **before** `diaClose.close()` here, whereas all other dialogs call `close()` first.

---

## 3. Control Hierarchy Summary

```
diaClose  (sap.m.Dialog)
│  modelSource      : WA_COMMENTS
│  title            : "Confirm Withdraw"
│  contentHeight    : 200px
│  contentWidth     : 500px
│  draggable        : true
│  verticalScrolling: false
│  leftButton       : butCloseCancel
│  rightButton      : butCloseSubmit
│  beforeOpen       : *(not set)*
│
├── oTextAreaCancel  (sap.m.TextArea)
│     value : {WA_COMMENTS>COMMENTS}
│     (placeholder/rows assumed: "Add Comments" / 8)
│
├── butCloseCancel  (sap.m.Button)
│     text  : "Cancel"
│     press : diaClose.close(); oApp.setBusy(false);
│
└── butCloseSubmit  (sap.m.Button)
      text  : "Withdraw"
      press : [see Section 2.3]
```

---

## 4. Model Dependencies

| Model             | Field      | Usage                                                        |
|-------------------|------------|--------------------------------------------------------------|
| `WA_COMMENTS`     | `COMMENTS` | TextArea value binding                                       |
| `modeloPageProj`  | `COMMENTS` | Written on Submit: `oTextAreaCancel.getValue()`              |

> No ITL flag sync. No `clear_value()` call. Simpler than `diaChange`/`diaCreate`/`diaSubmit`.

---

## 5. External Function Dependencies

| Function           | Location    | Purpose                            |
|--------------------|-------------|------------------------------------|
| `getOnlineClose()` | AJAX layer  | Fires the `CLOSE`/WITHDRAW AJAX    |

---

## 6. AJAX Reference

| AJAX Object | Trigger            | Condition          |
|-------------|--------------------|--------------------|
| `CLOSE`     | `getOnlineClose()` | Always — no branch |

---

## 7. Fiori Migration — XML View Fragment

```xml
<!-- diaClose Dialog -->
<Dialog
    id="diaClose"
    title="Confirm Withdraw"
    contentHeight="200px"
    contentWidth="500px"
    draggable="true"
    verticalScrolling="false"
    leftButton="butCloseCancel"
    rightButton="butCloseSubmit">

    <content>
        <TextArea
            id="oTextAreaCancel"
            placeholder="Add Comments"
            rows="8"
            value="{WA_COMMENTS>/COMMENTS}" />
    </content>

    <buttons>
        <Button
            id="butCloseCancel"
            text="Cancel"
            press=".onButCloseCancelPress" />
        <Button
            id="butCloseSubmit"
            text="Withdraw"
            press=".onButCloseSubmitPress" />
    </buttons>
</Dialog>
```

---

## 8. Fiori Migration — Controller JavaScript

```javascript
// No beforeOpen handler — TextArea is NOT cleared before opening (matches Neptune source)

onButCloseCancelPress: function () {
    this.byId("diaClose").close();
    // Explicitly release busy state
    this.getView().getModel("oAppModel").setProperty("/busy", false);
},

onButCloseSubmitPress: function () {
    var oView      = this.getView();
    var oPageModel = oView.getModel("modeloPageProj");

    // NOTE: setBusy(true) fires BEFORE close() — matches Neptune source order
    oView.getModel("oAppModel").setProperty("/busy", true);
    this.byId("diaClose").close();

    // Write comment from TextArea
    oPageModel.setProperty("/COMMENTS", this.byId("oTextAreaCancel").getValue());

    // Trigger CLOSE AJAX — no clear_value(), no ITL sync
    this._getOnlineClose();
},
```

---

## 9. Full Comparison Table — All Comment Dialogs

| Aspect                  | `diaChange`              | `diaReject`              | `diaCreate`                              | `diaSubmit`                         | `diaSubmitWthErr`              | `diaClose`                  |
|-------------------------|--------------------------|--------------------------|------------------------------------------|-------------------------------------|--------------------------------|-----------------------------|
| Title                   | *(none)*                 | `Reject Confirmation`    | `Confirmation to create new project request` | *(none)*                        | *(none)*                       | `Confirm Withdraw`          |
| contentHeight           | *(not set)*              | `200px`                  | `200px`                                  | *(not set)*                         | *(not set)*                    | `200px`                     |
| contentWidth            | *(not set)*              | `500px`                  | `500px`                                  | *(not set)*                         | *(not set)*                    | `500px`                     |
| draggable               | *(not set)*              | `true`                   | `true`                                   | *(not set)*                         | *(not set)*                    | `true`                      |
| verticalScrolling       | *(not set)*              | `false`                  | `false`                                  | *(not set)*                         | *(not set)*                    | `false`                     |
| TextArea control ID     | `oTextAreaChg`           | `oTextAreaReject`        | `oTextAreaCreate`                        | `oTextAreaSubmit`                   | `oTextAreaSubmitWthErr`        | `oTextAreaCancel`           |
| Save button text        | `Submit Change`          | `Reject`                 | `Create`                                 | `Submit`                            | `Submit`                       | `Withdraw`                  |
| Save button type        | *(not set)*              | *(not set)*              | `Accept`                                 | *(not set)*                         | *(not set)*                    | *(not set)*                 |
| Cancel press extra      | *(none)*                 | *(none)*                 | *(none)*                                 | `oApp.setBusy(false)`               | `oApp.setBusy(false)`          | `oApp.setBusy(false)`       |
| beforeOpen              | Clears TextArea          | Clears TextArea          | Clears TextArea                          | Clears TextArea                     | Clears TextArea                | **Not set**                 |
| setBusy order on Save   | close → busy             | close → busy             | close → busy                             | close → busy                        | close → busy                   | **busy → close**            |
| Comment validation      | None                     | Guards empty             | None                                     | None                                | None                           | None                        |
| ITL flag sync           | Yes                      | No                       | Yes                                      | No                                  | No                             | No                          |
| clear_value() called    | Yes                      | No                       | Yes                                      | Yes                                 | Yes                            | **No**                      |
| AJAX routing            | Single: Change           | Single: Reject           | Single: Create                           | Branched: Submit or NewProject      | Conditional: NewIntOrderWErr   | Single: Close               |

---

## 10. Migration Notes

1. **No `beforeOpen` — TextArea not cleared** — This is the only comment dialog with no `beforeOpen` handler. `oTextAreaCancel` retains its previous value when the dialog reopens. Preserve this exactly — do not add a `setValue("")` call.
2. **`setBusy(true)` before `close()`** — All other Save handlers call `close()` first then `setBusy(true)`. `diaClose` reverses this: `setBusy(true)` fires first. Preserve the order for 1:1 accuracy.
3. **No `clear_value()` call** — Unlike `diaChange`, `diaCreate`, `diaSubmit`, and `diaSubmitWthErr`, this dialog does not call `clear_value()` before firing AJAX. Do not add it.
4. **No ITL sync** — Same as `diaReject`, `diaSubmit`, `diaSubmitWthErr`.
5. **Dead code** — `//var newComments = modeldiaClose.oData.COMMENTS;` is commented out. Same pattern as `diaCreate` and `diaSubmit`. Active path reads from `oTextAreaCancel.getValue()`.
6. **TextArea named `oTextAreaCancel`** — The control ID uses "Cancel" despite being the withdrawal comment field. Do not confuse with the cancel button `butCloseCancel`. Keep the ID as-is for 1:1 replica.
7. **Save button text is `Withdraw`** — This is the semantic action. Consider SAP button type `Reject` or `Emphasized` in Fiori for visual clarity, but keep as-is for strict replica.
