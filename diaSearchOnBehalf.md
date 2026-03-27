# diaSearchOnBehalf – Dialog Screen Specification

> **Source PDF:** `Additional_Dialog_screen.pdf` (page 2–3)
> **Dialog Control ID:** `diaSearchOnBehalf`
> **Control Type:** `sap.m.SelectDialog`
> **Description:** `Search On Behalf Person`
> **Purpose:** Person lookup for the "On Behalf Of" field. Searches the `GT_AGENT_LIST` model and writes the selection into a dynamic `userField` reference.

---

## 1. Dialog Root — `sap.m.SelectDialog`

| Property     | Value                    |
|--------------|--------------------------|
| Name         | `diaSearchOnBehalf`      |
| Description  | `Search On Behalf Person`|
| Model Source | `GT_AGENT_LIST`          |
| Model Path   | *(empty)*                |
| Ajax ID      | *(not add to View)*      |

### 1.1 Events

| Event        | Handler             |
|--------------|---------------------|
| `confirm`    | Anonymous Function  |
| `liveChange` | *(not explicitly shown — inferred from tree)* |
| `search`     | Anonymous Function  |

---

## 2. List Item Template

| Child Control           | Name                  | Description         |
|-------------------------|-----------------------|---------------------|
| `sap.m.StandardListItem`| `lstOnBehalf_Person`  | `On Behalf Person`  |

> Title binding: `{GT_AGENT_LIST>TEXT}`

---

## 3. Event Handlers

### 3.1 `search` Event

```javascript
oApp.setBusy(true);

var searchTerm = oEvent.getParameter("value");
var binding = diaSearchOnBehalf.getBinding("items");
var filter = new sap.ui.model.Filter("TEXT", "Contains", searchTerm);

getOnlineGetOnBehalfPerson(filter.oValue1);
binding.filter([filter]);
```

---

### 3.2 `confirm` Event

```javascript
var entry = oEvent.getParameter("selectedItem").getBindingContext().getObject();

if (userField) {
    userField.setValue(entry.OBJID);
    userField.setDescription(entry.TEXT);
    userField.fireChange();
}
```

> **Key difference from `diaSearchAuthBy`:** The target input is not hardcoded. It uses the global `userField` variable (declared in `Global_Script.txt`) which is set dynamically before the dialog opens. This makes the dialog reusable for different input fields.

---

## 4. Model Dependencies

| Model           | Field  | Usage                                                         |
|-----------------|--------|---------------------------------------------------------------|
| `GT_AGENT_LIST` | `TEXT` | List item display; filter path; written as `setDescription`  |
| `GT_AGENT_LIST` | `OBJID`| Written via `userField.setValue()`                            |

---

## 5. Target Field Written On Confirm

| Target         | Method               | Value Source  |
|----------------|----------------------|---------------|
| `userField`    | `setValue()`         | `entry.OBJID` |
| `userField`    | `setDescription()`   | `entry.TEXT`  |

> `userField` is a global variable set before `diaSearchOnBehalf.open()` is called. It must point to the correct `sap.m.Input` at runtime.

---

## 6. AJAX References

| AJAX Object         | Trigger                             | Purpose                          |
|---------------------|-------------------------------------|----------------------------------|
| `GET_ONBEHALF_ID`   | `getOnlineGetOnBehalfPerson(term)`  | Backend search on `search` event |

---

## 7. Fiori Migration — XML View Fragment

```xml
<SelectDialog
    id="diaSearchOnBehalf"
    title="Search On Behalf Person"
    confirm=".onDiaSearchOnBehalfConfirm"
    search=".onDiaSearchOnBehalfSearch"
    items="{GT_AGENT_LIST>/}">
    <items>
        <StandardListItem
            id="lstOnBehalf_Person"
            title="{GT_AGENT_LIST>TEXT}" />
    </items>
</SelectDialog>
```

---

## 8. Fiori Migration — Controller JavaScript

```javascript
onDiaSearchOnBehalfSearch: function (oEvent) {
    var sValue   = oEvent.getParameter("value");
    var oBinding = this.byId("diaSearchOnBehalf").getBinding("items");
    var oFilter  = new sap.ui.model.Filter("TEXT", sap.ui.model.FilterOperator.Contains, sValue);

    this.getView().getModel("oAppModel").setProperty("/busy", true);
    this._getOnlineGetOnBehalfPerson(sValue);
    oBinding.filter([oFilter]);
},

onDiaSearchOnBehalfConfirm: function (oEvent) {
    var oEntry = oEvent.getParameter("selectedItem").getBindingContext("GT_AGENT_LIST").getObject();

    if (this._userField) {
        this._userField.setValue(oEntry.OBJID);
        this._userField.setDescription(oEntry.TEXT);
        this._userField.fireChange();
    }
},
```

---

## 9. Migration Notes

1. **`userField` global pattern** — Neptune uses a module-level `var userField` (Global_Script.txt) as a pointer to the target input. In Fiori, store the reference as `this._userField` on the controller before opening the dialog.
2. **`setValue(OBJID)` not `TEXT`** — Unlike `diaSearchAuthBy` which sets the display text on the input, this sets the OBJID as the input value and TEXT as the description. The target input must be an `sap.m.Input` with a description field (or a Value Help field).
3. **No `modeloPageProj` write** — Unlike most other search dialogs, this one writes only to `userField`. There is no direct `modeloPageProj` property update in the confirm handler.
4. **No `getOnlineUpdate...` AJAX on confirm** — Only the `search` event fires AJAX. Confirm is pure UI update.
