# diaSearchAuthBy – Dialog Screen Specification

> **Source PDF:** `Additional_Dialog_screen.pdf` (page 1–2)
> **Dialog Control ID:** `diaSearchAuthBy`
> **Control Type:** `sap.m.SelectDialog`
> **Description:** `Search Authorized By`
> **Purpose:** Person lookup for the "Authorized By" field. Searches the `GT_AUTH_BY_LIST` model and writes the selected person's ID and text back to `modeloPageProj` and `inpAuthBy`.

---

## 1. Dialog Root — `sap.m.SelectDialog`

| Property     | Value                |
|--------------|----------------------|
| Name         | `diaSearchAuthBy`    |
| Description  | `Search Authorized By` |
| Model Source | `GT_AUTH_BY_LIST`    |
| Model Path   | *(empty)*            |
| Ajax ID      | *(not add to View)*  |

### 1.1 Events

| Event        | Handler             |
|--------------|---------------------|
| `confirm`    | Anonymous Function  |
| `liveChange` | Anonymous Function  |
| `search`     | Anonymous Function  |

---

## 2. List Item Template

| Child Control      | Name                  | Description        |
|--------------------|-----------------------|--------------------|
| `sap.m.StandardListItem` | *(declared as child)* | Bound to `GT_AUTH_BY_LIST` |

> Title binding: `{GT_AUTH_BY_LIST>TEXT}`

---

## 3. Event Handlers

### 3.1 `search` Event

```javascript
oApp.setBusy(true);

var searchTerm = oEvent.getParameter("value");
var binding = diaSearchAuthBy.getBinding("items");
var filter = new sap.ui.model.Filter("TEXT", "Contains", searchTerm);

getOnlineGetAuthorizedBy(filter.oValue1);
binding.filter([filter]);
```

> **Note:** Calls backend AJAX `getOnlineGetAuthorizedBy()` with the raw filter value, then also applies the filter to the local binding. Backend refreshes the list model; local filter provides immediate UI feedback.

---

### 3.2 `liveChange` Event

```javascript
var searchTerm = oEvent.getParameter("value");
var binding = diaSearchAuthBy.getBinding("items");
var filter = new sap.ui.model.Filter("TEXT", "Contains", searchTerm);
binding.filter([filter]);
```

> Client-side only — no AJAX call. Filters the already-loaded list as the user types.

---

### 3.3 `confirm` Event

```javascript
var entry = oEvent.getParameter("selectedItem").getBindingContext().getObject();

inpAuthBy.setValue(entry.TEXT);
modeloPageProj.oData.AUTH_BY_ID = entry.OBJID;
modeloPageProj.refresh();

getOnlineUpdateAuthorizedBy(entry.OBJID);
inpAuthBy.fireChange();
```

---

## 4. Model Dependencies

| Model            | Field       | Usage                                              |
|------------------|-------------|----------------------------------------------------|
| `GT_AUTH_BY_LIST`| `TEXT`      | List item display text; filter path                |
| `GT_AUTH_BY_LIST`| `OBJID`     | Written to `modeloPageProj.oData.AUTH_BY_ID`       |
| `modeloPageProj` | `AUTH_BY_ID`| Stores selected person object ID                  |

---

## 5. Target Field Written On Confirm

| Control     | Property Set        | Value Source  |
|-------------|---------------------|---------------|
| `inpAuthBy` | `setValue()`        | `entry.TEXT`  |
| `modeloPageProj` | `AUTH_BY_ID`   | `entry.OBJID` |

---

## 6. AJAX References

| AJAX Object          | Trigger                            | Purpose                             |
|----------------------|------------------------------------|-------------------------------------|
| `GET_AUTHBY_ID`      | `getOnlineGetAuthorizedBy(term)`   | Backend search on `search` event    |
| `UPDATE_AUTHORIZED_BY` | `getOnlineUpdateAuthorizedBy(id)` | Persists selection on `confirm`     |

---

## 7. Fiori Migration — XML View Fragment

```xml
<SelectDialog
    id="diaSearchAuthBy"
    title="Search Authorized By"
    confirm=".onDiaSearchAuthByConfirm"
    liveChange=".onDiaSearchAuthByLiveChange"
    search=".onDiaSearchAuthBySearch"
    items="{GT_AUTH_BY_LIST>/}">
    <items>
        <StandardListItem
            title="{GT_AUTH_BY_LIST>TEXT}" />
    </items>
</SelectDialog>
```

---

## 8. Fiori Migration — Controller JavaScript

```javascript
onDiaSearchAuthBySearch: function (oEvent) {
    var sValue = oEvent.getParameter("value");
    var oBinding = this.byId("diaSearchAuthBy").getBinding("items");
    var oFilter = new sap.ui.model.Filter("TEXT", sap.ui.model.FilterOperator.Contains, sValue);

    // Set app busy and trigger backend call
    this.getView().getModel("oAppModel").setProperty("/busy", true);
    this._getOnlineGetAuthorizedBy(sValue);
    oBinding.filter([oFilter]);
},

onDiaSearchAuthByLiveChange: function (oEvent) {
    var sValue = oEvent.getParameter("value");
    var oBinding = this.byId("diaSearchAuthBy").getBinding("items");
    var oFilter = new sap.ui.model.Filter("TEXT", sap.ui.model.FilterOperator.Contains, sValue);
    oBinding.filter([oFilter]);
},

onDiaSearchAuthByConfirm: function (oEvent) {
    var oEntry    = oEvent.getParameter("selectedItem").getBindingContext("GT_AUTH_BY_LIST").getObject();
    var oPageModel = this.getView().getModel("modeloPageProj");

    this.byId("inpAuthBy").setValue(oEntry.TEXT);
    oPageModel.setProperty("/AUTH_BY_ID", oEntry.OBJID);

    this._getOnlineUpdateAuthorizedBy(oEntry.OBJID);
    this.byId("inpAuthBy").fireChange();
},
```

---

## 9. Migration Notes

1. **`sap.m.SelectDialog` not `sap.m.Dialog`** — This is a `SelectDialog`, not a standard dialog. It has built-in search bar, list, and confirm/cancel buttons. No explicit `leftButton`/`rightButton` needed.
2. **Dual filter strategy** — `search` event fires both a backend AJAX call AND a local binding filter. `liveChange` does local filter only.
3. **`filter.oValue1`** — Neptune passes `filter.oValue1` (the raw string) directly to the AJAX function. In Fiori, pass `sValue` directly.
4. **`inpAuthBy.fireChange()`** — Fires the change event on the target input after setting value, to trigger any downstream dirty/validation handlers.
5. **`getOnlineUpdateAuthorizedBy`** — Called on every confirm, even if the same person is re-selected. No deduplication in Neptune source.
