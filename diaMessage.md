# diaMessage – Dialog Screen Specification

> **Source PDF:** `diaMessage.pdf`
> **Dialog Control ID:** `diaMessage`
> **Control Type:** `sap.m.Dialog`
> **Purpose:** Generic message/notification dialog. Displays a message to the user and releases the app busy state on close. Used as a post-AJAX notification overlay.

---

## 1. Dialog Root — `sap.m.Dialog`

| Property          | Value               |
|-------------------|---------------------|
| Name              | `diaMessage`        |
| Description       | *(none)*            |
| Model Source      | *(none set)*        |
| Model Path        | *(empty)*           |
| Ajax ID           | *(not add to View)* |
| rightButton       | `butMessageClose`   |

### 1.1 Associations

| Association   | Value             |
|---------------|-------------------|
| `rightButton` | `butMessageClose` |

### 1.2 Events

| Event        | Handler        |
|--------------|----------------|
| `afterClose` | *(not set)*    |
| `afterOpen`  | *(not set)*    |
| `beforeClose`| *(not set)*    |
| `beforeOpen` | *(not set)*    |

> No `beforeOpen` handler — no pre-open setup required.

---

## 2. Child Controls

### 2.1 `butMessageClose` — `sap.m.Button`

| Property | Value              |
|----------|--------------------|
| Name     | `butMessageClose`  |

#### Press Event Handler

```javascript
diaMessage.close();
oApp.setBusy(false);
```

> **Key behaviour:** Closing this dialog explicitly releases `oApp.setBusy(false)`. This confirms `diaMessage` is shown after an AJAX operation completes (the app was left busy while the message was displayed). Releasing busy on close returns the UI to interactive state.

---

## 3. Control Hierarchy Summary

```
diaMessage  (sap.m.Dialog)
│  rightButton : butMessageClose
│
├── [message content — text/state injected at runtime]
│
└── butMessageClose  (sap.m.Button)
      press : diaMessage.close(); oApp.setBusy(false);
```

> The dialog's message content (text, title, state) is set programmatically at runtime before `diaMessage.open()` is called — not via static bindings in the view. See Section 4.

---

## 4. Runtime Usage Pattern

`diaMessage` is a reusable notification overlay. In Neptune, the `showMessage()` utility in `Utilities.txt` creates inline `sap.m.Dialog` instances dynamically. However, `diaMessage` in the view is a **persistent** dialog instance that gets its content set before opening. Typical usage pattern:

```javascript
// Set content before opening (inferred from usage context)
diaMessage.setTitle("Attention");
diaMessage.setState(sap.ui.core.ValueState.Error);  // or Success, Warning, None
// Set message text on the content Text control
oTextMessage.setText("Your message here");
diaMessage.open();
```

> Compare with the standalone `showMessage()` utility function in `Utilities.txt` which creates a *new* dialog on each call. `diaMessage` is the view-declared persistent equivalent used in AJAX success/error callbacks.

---

## 5. Relationship to `showMessage()` Utility

`Utilities.txt` contains a separate `showMessage()` function that dynamically creates dialogs:

```javascript
function showMessage(message, state) {
    var msgDialog = new sap.m.Dialog({
        state: state,
        title: "Attention",
        endButton: new sap.m.Button({
            icon: "sap-icon://sys-cancel",
            press: function(evt) { evt.oSource.getParent().close(); }
        }),
        content: new sap.m.Text({ text: message })
    });
    msgDialog.open();
}
```

| Aspect               | `diaMessage` (view-declared)        | `showMessage()` (dynamic)              |
|----------------------|-------------------------------------|----------------------------------------|
| Instance type        | Persistent, declared in view        | New instance per call                  |
| Close behaviour      | `setBusy(false)` on close           | Just closes — no busy release          |
| Button               | `butMessageClose` (named)           | Anonymous `endButton` with cancel icon |
| Content binding      | Set programmatically at runtime     | Passed as `message` parameter          |
| Use case             | Post-AJAX result notifications      | Inline validation / error messages     |

---

## 6. Fiori Migration — XML View Fragment

```xml
<!-- diaMessage Dialog -->
<Dialog
    id="diaMessage"
    rightButton="butMessageClose">

    <content>
        <Text id="oTextMessage" text="" />
    </content>

    <buttons>
        <Button
            id="butMessageClose"
            text="Close"
            press=".onButMessageClosePress" />
    </buttons>
</Dialog>
```

---

## 7. Fiori Migration — Controller JavaScript

```javascript
// Helper to open the message dialog with content
_showDiaMessage: function (sTitle, sMessage, sState) {
    var oDialog = this.byId("diaMessage");
    oDialog.setTitle(sTitle || "Attention");
    oDialog.setState(sState || sap.ui.core.ValueState.None);
    this.byId("oTextMessage").setText(sMessage);
    oDialog.open();
},

onButMessageClosePress: function () {
    this.byId("diaMessage").close();
    // Explicitly release app busy state
    this.getView().getModel("oAppModel").setProperty("/busy", false);
},
```

---

## 8. Migration Notes

1. **`setBusy(false)` on close** — The close button always releases the busy state. This dialog is shown while the app is busy (post-AJAX). Preserve this behaviour exactly — do not remove the `setBusy(false)` call.
2. **No static content binding** — The dialog's title, state, and message text are set at runtime before opening. There is no `value="{...}"` binding in the view XML. The `oTextMessage` Text control starts with `text=""`.
3. **`rightButton` only** — Single close button on the right. No left/cancel button.
4. **No `beforeOpen` handler** — Content must be set via the helper method before calling `open()`.
5. **Distinct from `showMessage()`** — The dynamic `showMessage()` utility in `Utilities.txt` does NOT release busy. `diaMessage` DOES. Use `diaMessage` for post-AJAX results; use `_showDiaMessage` wrapper (or the utility) for inline validation messages.
6. **Button text not confirmed** — The PDF does not show the button text for `butMessageClose`. Likely "Close" or "OK". Check source XML or use "Close" as default.
7. **`state` property** — The dialog `state` property (Error/Warning/Success/None) controls the header color. Set this at runtime to match the AJAX outcome (error = red, success = green).
