# diaSearchCostCenter – Dialog Screen Specification

> **Source PDF:** `Additional_Dialog_screen.pdf` (page 4–5)
> **Dialog Control ID:** `diaSearchCostCenter`
> **Control Type:** `sap.m.SelectDialog`
> **Description:** `Search On CostCenter`
> **Purpose:** Cost center lookup. Writes selection to either the primary `inpCostCenter` or `inpMultiCostCenter` depending on whether `diaAddMultiCC` is open.

---

## 1. Dialog Root — `sap.m.SelectDialog`

| Property     | Value                   |
|--------------|-------------------------|
| Name         | `diaSearchCostCenter`   |
| Description  | `Search On CostCenter`  |
| Model Source | `GT_KOSTL`              |
| Model Path   | *(empty)*               |
| Ajax ID      | *(not add to View)*     |

### 1.1 Events

| Event        | Handler             |
|--------------|---------------------|
| `confirm`    | Anonymous Function  |
| `liveChange` | Anonymous Function  |
| `search`     | Anonymous Function  |

---

## 2. Event Handlers

### 2.1 `confirm` Event

```javascript
var entry = oEvent.getParameter("selectedItem").getBindingContext().getObject();

if (!diaAddMultiCC.isOpen()) {
    inpCostCenter.setValue(entry.TEXT);
    modeloPageProj.oData.KOSTL = entry.VALUE;
    modeloPageProj.refresh();
    inpCostCenter.fireChange();
} else {
    inpMultiCostCenter.setValue(entry.TEXT);
    //modelodiaAddMultiCC.oData.KOSTL_CODE = entry.VALUE;
    //modelodiaAddMultiCC.refresh();
    inpMultiCostCenter.fireChange();
}
```

> **Branching logic:** If `diaAddMultiCC` (the multi-cost-center dialog) is open, the selection goes to `inpMultiCostCenter` and does NOT write to `modeloPageProj`. The two commented-out lines show that model write for the multi-CC path was intentionally removed.

---

### 2.2 `liveChange` Event

```javascript
var searchTerm = oEvent.getParameter("value");
var binding = diaSearchCostCenter.getBinding("items");
var filter = new sap.ui.model.Filter("TEXT", "Contains", searchTerm);
binding.filter([filter]);
```

---

### 2.3 `search` Event

```javascript
oApp.setBusy(true);

var searchTerm = oEvent.getParameter("value");
////////// ;

var binding = diaSearchCostCenter.getBinding("items");
var filter = new sap.ui.model.Filter("TEXT", "Contains", searchTerm);

getOnlineGetCostCenter(filter.oValue1);
binding.filter([filter]);
```

> `////////// ;` is a Neptune debugger breakpoint marker in the source — ignore in migration.

---

## 3. Model Dependencies

| Model            | Field   | Usage                                                      |
|------------------|---------|------------------------------------------------------------|
| `GT_KOSTL`       | `TEXT`  | List display text; filter path; written to `inpCostCenter` |
| `GT_KOSTL`       | `VALUE` | Written to `modeloPageProj.oData.KOSTL` (primary path)     |
| `modeloPageProj` | `KOSTL` | Updated on confirm (primary path only)                     |

---

## 4. Target Fields Written On Confirm

| Condition               | Target Control       | Property Set   | Value Source  |
|-------------------------|----------------------|----------------|---------------|
| `diaAddMultiCC` closed  | `inpCostCenter`      | `setValue()`   | `entry.TEXT`  |
| `diaAddMultiCC` closed  | `modeloPageProj`     | `KOSTL`        | `entry.VALUE` |
| `diaAddMultiCC` open    | `inpMultiCostCenter` | `setValue()`   | `entry.TEXT`  |

---

## 5. AJAX References

| AJAX Object       | Trigger                          | Purpose                          |
|-------------------|----------------------------------|----------------------------------|
| `GET_COST_CENTER` | `getOnlineGetCostCenter(term)`   | Backend search on `search` event |

---

## 6. Fiori Migration — XML View Fragment

```xml
<SelectDialog
    id="diaSearchCostCenter"
    title="Search On CostCenter"
    confirm=".onDiaSearchCostCenterConfirm"
    liveChange=".onDiaSearchCostCenterLiveChange"
    search=".onDiaSearchCostCenterSearch"
    items="{GT_KOSTL>/}">
    <items>
        <StandardListItem
            title="{GT_KOSTL>TEXT}" />
    </items>
</SelectDialog>
```

---

## 7. Fiori Migration — Controller JavaScript

```javascript
onDiaSearchCostCenterSearch: function (oEvent) {
    var sValue   = oEvent.getParameter("value");
    var oBinding = this.byId("diaSearchCostCenter").getBinding("items");
    var oFilter  = new sap.ui.model.Filter("TEXT", sap.ui.model.FilterOperator.Contains, sValue);

    this.getView().getModel("oAppModel").setProperty("/busy", true);
    this._getOnlineGetCostCenter(sValue);
    oBinding.filter([oFilter]);
},

onDiaSearchCostCenterLiveChange: function (oEvent) {
    var sValue   = oEvent.getParameter("value");
    var oBinding = this.byId("diaSearchCostCenter").getBinding("items");
    var oFilter  = new sap.ui.model.Filter("TEXT", sap.ui.model.FilterOperator.Contains, sValue);
    oBinding.filter([oFilter]);
},

onDiaSearchCostCenterConfirm: function (oEvent) {
    var oEntry     = oEvent.getParameter("selectedItem").getBindingContext("GT_KOSTL").getObject();
    var oPageModel = this.getView().getModel("modeloPageProj");
    var oDiaMultiCC = this.byId("diaAddMultiCC");

    if (!oDiaMultiCC || !oDiaMultiCC.isOpen()) {
        this.byId("inpCostCenter").setValue(oEntry.TEXT);
        oPageModel.setProperty("/KOSTL", oEntry.VALUE);
        this.byId("inpCostCenter").fireChange();
    } else {
        this.byId("inpMultiCostCenter").setValue(oEntry.TEXT);
        // NOTE: model write for multi-CC path was intentionally removed in Neptune source
        this.byId("inpMultiCostCenter").fireChange();
    }
},
```

---

## 8. Migration Notes

1. **Dual-target confirm** — The same dialog writes to different inputs based on which parent dialog is open. Preserve the `diaAddMultiCC.isOpen()` check.
2. **Commented-out multi-CC model write** — `modelodiaAddMultiCC.oData.KOSTL_CODE = entry.VALUE` was intentionally commented out. Do not restore it.
3. **Debugger marker** — `////////// ;` in the `search` handler is a Neptune source artifact. Remove in Fiori.
4. **`GT_KOSTL` filter uses `TEXT` only** — Unlike `diaGeography`, `diaBrand`, `diaLocation` which filter on both `TEXT` and `VALUE`/`LOCATION`, this uses a simple single-path filter on `TEXT`.
