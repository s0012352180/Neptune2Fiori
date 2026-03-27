# diaDelDraft – Dialog Screen Specification

> **Source PDF:** `diaDelDraft.pdf`  
> **Dialog Control ID:** `diaDelDraft`  
> **Purpose:** Simple confirmation dialog to delete a draft project request. No comment TextArea — user confirms or cancels only.

---

## 1. Dialog Root — `sap.m.Dialog`

| Property          | Value               |
|-------------------|---------------------|
| Name              | `diaDelDraft`       |
| Description       | *(none)*            |
| Model Source      | `GS_PROJ_FORM`      |
| Model Path        | *(empty)*           |
| Ajax ID           | *(not add to View)* |
| leftButton        | `butDDraftCancel`   |
| rightButton       | `butDDraft`         |

### 1.1 Events

| Event        | Handler                         |
|--------------|---------------------------------|
| `beforeOpen` | *(not set)*                     |

> **Note:** No `beforeOpen` event and **no TextArea child**. This is a pure confirmation dialog — the only one in the comment/action dialog group with no text input.  
> **Model Source is `GS_PROJ_FORM`** — different from all other comment dialogs which use `WA_COMMENTS`. This gives the dialog access to form-level data (e.g. project name/ID) for display in the confirmation text.

### 1.2 Inline Code Visible on Page 1

The Neptune Cockpit code editor tab shows two lines associated with the dialog open trigger:
```javascript
diaDelDraft.open();
oApp.setBusy();
```
This confirms the app is set busy **before or at the point** the dialog is opened, consistent with the cancel handler releasing busy.

---

## 2. Child Controls

### 2.1 `sDDraftText` — `sap.m.Text`

| Property | Value                      |
|----------|----------------------------|
| Name     | `sDDraftText`              |
| Do not add to View | `true` (only declare) |
| text     | `Confirm delete draft?`    |

> This is a static confirmation message — no binding, no input. Replaces the TextArea seen in all other dialogs.

---

### 2.2 `butDDraft` — `sap.m.Button`

| Property | Value           |
|----------|-----------------|
| Name     | `butDDraft`     |
| text     | `Delete Draft`  |

#### Press Event Handler (full source)

```javascript
// Close the dialog
diaDelDraft.close();
getOnlineDeleteDraft();
```

> **Note:** No `oApp.setBusy(true)` call on the confirm button. The app was already set busy when the dialog was opened (see inline code above). No `modeloPageProj` writes, no `clear_value()`, no COMMENTS update — the simplest Save handler in the entire dialog set.

---

### 2.3 `butDDraftCancel` — `sap.m.Button`

| Property | Value            |
|----------|------------------|
| Name     | `butDDraftCancel`|
| text     | `Cancel`         |

#### Press Event Handler (full source — from page 3 code editor)

```javascript
// Close the dialog
diaDelDraft.close();
```

> **Note:** Unlike `diaSubmit`, `diaSubmitWthErr`, and `diaClose`, this cancel does **not** call `oApp.setBusy(false)`. The busy state management is handled externally.

---

## 3. Control Hierarchy Summary

```
diaDelDraft  (sap.m.Dialog)
│  modelSource  : GS_PROJ_FORM
│  leftButton   : butDDraftCancel
│  rightButton  : butDDraft
│  beforeOpen   : *(not set)*
│
├── sDDraftText  (sap.m.Text)
│     text : "Confirm delete draft?"
│
├── butDDraftCancel  (sap.m.Button)
│     text  : "Cancel"
│     press : diaDelDraft.close();
│
└── butDDraft  (sap.m.Button)
      text  : "Delete Draft"
      press : diaDelDraft.close(); getOnlineDeleteDraft();
```

---

## 4. Model Dependencies

| Model          | Usage                                                    |
|----------------|----------------------------------------------------------|
| `GS_PROJ_FORM` | Dialog model source — provides form context for display  |

> No writes to `modeloPageProj`. No `WA_COMMENTS` binding. This dialog is read-only from a data perspective.

---

## 5. External Function Dependencies

| Function                 | Location    | Purpose                              |
|--------------------------|-------------|--------------------------------------|
| `getOnlineDeleteDraft()` | AJAX layer  | Fires the `DELETE_DRAFT` AJAX call   |

---

## 6. AJAX Reference

| AJAX Object     | Trigger                   | Condition          |
|-----------------|---------------------------|--------------------|
| `DELETE_DRAFT`  | `getOnlineDeleteDraft()`  | Always — no branch |

---

## 7. Fiori Migration — XML View Fragment

```xml
<!-- diaDelDraft Dialog -->
<Dialog
    id="diaDelDraft"
    leftButton="butDDraftCancel"
    rightButton="butDDraft">

    <content>
        <Text
            id="sDDraftText"
            text="Confirm delete draft?" />
    </content>

    <buttons>
        <Button
            id="butDDraftCancel"
            text="Cancel"
            press=".onButDDraftCancelPress" />
        <Button
            id="butDDraft"
            text="Delete Draft"
            press=".onButDDraftPress" />
    </buttons>
</Dialog>
```

---

## 8. Fiori Migration — Controller JavaScript

```javascript
// No beforeOpen handler needed

onButDDraftCancelPress: function () {
    this.byId("diaDelDraft").close();
    // NOTE: No setBusy(false) — matches Neptune source
},

onButDDraftPress: function () {
    // NOTE: No setBusy(true) here — app was already set busy at dialog open
    this.byId("diaDelDraft").close();
    this._getOnlineDeleteDraft();
},
```

---

## 9. How This Dialog Differs From All Others

| Aspect                    | All Other Comment Dialogs          | `diaDelDraft`                         |
|---------------------------|------------------------------------|---------------------------------------|
| Content child             | `sap.m.TextArea` (comment input)   | `sap.m.Text` (static message only)    |
| Model Source              | `WA_COMMENTS`                      | `GS_PROJ_FORM`                        |
| beforeOpen                | Most clear TextArea                | Not set                               |
| Writes to `modeloPageProj`| Yes (COMMENTS, sometimes ITL)      | **No writes**                         |
| `clear_value()` called    | Most do                            | **No**                                |
| `setBusy(true)` on Save   | Yes — all                          | **No — already busy at open**         |
| `setBusy(false)` on Cancel| `diaSubmit`, `diaSubmitWthErr`, `diaClose` | **No**                       |
| AJAX routing              | Varies                             | Single unconditional: `DeleteDraft`   |
| User input required       | Yes — comment text                 | **No — confirm only**                 |

---

## 10. Migration Notes

1. **No TextArea — pure confirmation** — This dialog asks the user to confirm deletion only. There is no comment field. Do not add a TextArea.
2. **Model Source is `GS_PROJ_FORM`** — Unlike all comment dialogs that use `WA_COMMENTS`, this dialog binds to `GS_PROJ_FORM`. In Fiori, set the dialog's model binding to the `GS_PROJ_FORM` named model, or pass relevant project data into the dialog context before opening.
3. **App set busy at open, not on confirm** — `oApp.setBusy()` is called when the dialog is opened (from the trigger code), not inside the confirm button handler. In Fiori, set busy in the method that calls `diaDelDraft.open()`, not in `onButDDraftPress`.
4. **Cancel does not release busy** — Unlike `diaSubmit`, `diaSubmitWthErr`, and `diaClose`, this cancel only closes the dialog. Busy state management is the responsibility of the caller. Ensure the trigger method handles busy release on cancel if needed.
5. **Static text child, not a Label** — The confirmation message is a `sap.m.Text` control (`sDDraftText`), not a `sap.m.Label`. Preserve the control type in Fiori.
6. **No title set** — The dialog has no `title` property set. It renders with default/no header title. Do not add one for a strict 1:1 replica.
