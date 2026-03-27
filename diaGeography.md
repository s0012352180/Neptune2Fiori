# diaGeography – Dialog Screen Specification

> **Source PDF:** `Additional_Dialog_screen.pdf` (page 5–6)
> **Dialog Control ID:** `diaGeography`
> **Control Type:** `sap.m.SelectDialog`
> **Description:** *(none)*
> **Purpose:** Geography lookup. Writes selected geography value to `modeloPageProj.GEOGRAPHY`.

---

## 1. Dialog Root — `sap.m.SelectDialog`

| Property     | Value               |
|--------------|---------------------|
| Name         | `diaGeography`      |
| Description  | *(none)*            |
| Model Source | `GT_GEOGRAPHYLIST`  |
| Model Path   | *(empty)*           |
| Ajax ID      | *(not add to View)* |

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
modeloPageProj.setProperty("/GEOGRAPHY", entry.VALUE);
```

> **Simplest confirm handler in all search dialogs** — one line, no input field update, no fireChange(), no AJAX.

---

### 2.2 `liveChange` Event

```javascript
var searchTerm = oEvent.getParameter("value");
var binding = oEvent.oSource.getBinding("items");

var filter = [];
if (typeof searchTerm === "string" && searchTerm.trim().length > 0) {
    filter = new sap.ui.model.Filter({
        filters: [
            new sap.ui.model.Filter({path: "TEXT",  operator: "Contains",   value1: searchTerm}),
            new sap.ui.model.Filter({path: "VALUE", operator: "StartsWith", value1: searchTerm})
        ]
    });
}
binding.filter([filter]);
```

> Uses `oEvent.oSource` (not a hardcoded dialog reference) — reusable pattern.  
> Dual-path filter: matches on **TEXT** (Contains) OR **VALUE** (StartsWith).  
> Empty search clears the filter (resets to full list).

---

### 2.3 `search` Event

```javascript
var searchTerm = oEvent.getParameter("value");
var binding = oEvent.oSource.getBinding("items");

var filter = [];
if (typeof searchTerm === "string" && searchTerm.trim().length > 0) {
    filter = new sap.ui.model.Filter({
        filters: [
            new sap.ui.model.Filter({path: "TEXT",  operator: "Contains",   value1: searchTerm}),
            new sap.ui.model.Filter({path: "VALUE", operator: "StartsWith", value1: searchTerm})
        ]
    });
}
binding.filter([filter]);
```

> **No `oApp.setBusy(true)` and no backend AJAX call** — purely client-side filter. Identical logic to `liveChange`.

---

## 3. Model Dependencies

| Model             | Field      | Usage                                      |
|-------------------|------------|--------------------------------------------|
| `GT_GEOGRAPHYLIST`| `TEXT`     | List display; filter path (Contains)       |
| `GT_GEOGRAPHYLIST`| `VALUE`    | Filter path (StartsWith); written on confirm |
| `modeloPageProj`  | `GEOGRAPHY`| Written on confirm: `entry.VALUE`          |

---

## 4. Fiori Migration — XML View Fragment

```xml
<SelectDialog
    id="diaGeography"
    confirm=".onDiaGeographyConfirm"
    liveChange=".onDiaGeographyLiveChange"
    search=".onDiaGeographySearch"
    items="{GT_GEOGRAPHYLIST>/}">
    <items>
        <StandardListItem
            title="{GT_GEOGRAPHYLIST>TEXT}"
            description="{GT_GEOGRAPHYLIST>VALUE}" />
    </items>
</SelectDialog>
```

---

## 5. Fiori Migration — Controller JavaScript

```javascript
_applyGeographyFilter: function (oEvent) {
    var sValue   = oEvent.getParameter("value");
    var oBinding = oEvent.getSource().getBinding("items");
    var oFilter  = [];

    if (typeof sValue === "string" && sValue.trim().length > 0) {
        oFilter = new sap.ui.model.Filter({
            filters: [
                new sap.ui.model.Filter({path: "TEXT",  operator: sap.ui.model.FilterOperator.Contains,    value1: sValue}),
                new sap.ui.model.Filter({path: "VALUE", operator: sap.ui.model.FilterOperator.StartsWith,  value1: sValue})
            ]
        });
    }
    oBinding.filter([oFilter]);
},

onDiaGeographyLiveChange: function (oEvent) { this._applyGeographyFilter(oEvent); },
onDiaGeographySearch:     function (oEvent) { this._applyGeographyFilter(oEvent); },

onDiaGeographyConfirm: function (oEvent) {
    var oEntry = oEvent.getParameter("selectedItem").getBindingContext("GT_GEOGRAPHYLIST").getObject();
    this.getView().getModel("modeloPageProj").setProperty("/GEOGRAPHY", oEntry.VALUE);
},
```

---

## 6. Migration Notes

1. **No backend AJAX on search** — Both `search` and `liveChange` are client-side only. No `setBusy`, no AJAX call. The full geography list is pre-loaded into `GT_GEOGRAPHYLIST`.
2. **Dual-path composite filter** — Filters on both `TEXT` (Contains) and `VALUE` (StartsWith). This is an OR composite filter — match either condition. Implement with `sap.ui.model.FilterOperator` enum in Fiori.
3. **Empty search resets filter** — When `searchTerm` is empty or non-string, `filter = []` is passed to `binding.filter([])`, which clears all filters and shows the full list.
4. **`oEvent.oSource` pattern** — Neptune uses `oEvent.oSource` (internal Neptune property). In Fiori use `oEvent.getSource()`.
5. **Confirm writes `VALUE` not `TEXT`** — Unlike name-lookup dialogs that write `TEXT` to a display field, this writes `entry.VALUE` directly to the model. No input field is updated.
