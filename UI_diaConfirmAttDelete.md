# UI Component: diaConfirmAttDelete
**App:** ZDCI_NAD_PROJECT_REQUEST
**Type:** sap.m.Dialog
**Model Source:** WA_CONFIRM_DELETE
**Purpose:** Confirmation dialog before deleting an attachment — two-button pattern (Yes / No).

---

## Control Hierarchy

```
diaConfirmAttDelete (sap.m.Dialog)
  ├── txtConfirmAttDelete (sap.m.Text)       — confirmation message
  ├── btnAttDeleteYes (sap.m.Button)         — beginButton / Yes
  └── btnAttDeleteNo (sap.m.Button)          — endButton / No (Cancel)
```

---

## Dialog Attributes

### diaConfirmAttDelete — sap.m.Dialog
| Attribute | Value |
|-----------|-------|
| Model Source | WA_CONFIRM_DELETE |
| beginButton (Association) | btnAttDeleteNo |
| endButton (Association) | btnAttDeleteYes |

### txtConfirmAttDelete — sap.m.Text
| Attribute | Value |
|-----------|-------|
| text | Are you certain you want to delete the selected attachment? |

### btnAttDeleteYes — sap.m.Button
| Attribute | Value |
|-----------|-------|
| icon | sap-icon:delete |
| text | Yes |
| type | Reject |

### btnAttDeleteNo — sap.m.Button
| Attribute | Value |
|-----------|-------|
| press | `diaConfirmAttDelete.close()` |

---

## Button Press Code

### btnAttDeleteYes — press (Confirm Delete)
```js
diaConfirmAttDelete.close();

// Read the stored index set when delete was triggered from the list
var deleteIndex = modeldiaConfirmAttDelete.oData.DELETE_INDEX;
var attachment  = modellistAttachments.oData[deleteIndex];

if (attachment.UUID && attachment.UUID.trim() !== "") {
    // Attachment already saved in SAP — call backend to delete
    getOnlineDeleteAttachment();
} else {
    // Not yet saved in SAP — remove from frontend model only
    modellistAttachments.oData.splice(deleteIndex, 1);
    modellistAttachments.updateBindings();
    diaConfirmAttDelete.close();
}
```

### btnAttDeleteNo — press (Cancel)
```js
diaConfirmAttDelete.close();
```

---

## Notes
- `DELETE_INDEX` is set on `modeldiaConfirmAttDelete` by `listAttachments delete` event before dialog opens
- Two-path delete logic:
  - **UUID exists** → attachment is in SAP → call `getOnlineDeleteAttachment()` to remove from backend
  - **No UUID** → attachment only on frontend (just uploaded, not yet saved) → splice from model array and refresh bindings
- `btnAttDeleteYes` type is `Reject` — renders as red destructive button in SAP Fiori
- `btnAttDeleteNo` simply closes the dialog — no state change
- Model used: `modeldiaConfirmAttDelete` (holds `DELETE_INDEX`), `modellistAttachments` (attachment array)
- Helper: `getOnlineDeleteAttachment()`
