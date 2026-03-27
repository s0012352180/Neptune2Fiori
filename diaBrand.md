# diaBrand – Dialog Screen Specification

> **Source PDF:** `Additional_Dialog_screen.pdf` (page 6–7)
> **Dialog Control ID:** `diaBrand`
> **Control Type:** `sap.m.SelectDialog`
> **Description:** *(none)*
> **Purpose:** Brand lookup. Writes selected brand value to `modeloPageProj.BRAND`.

---

## 1. Dialog Root — `sap.m.SelectDialog`

| Property     | Value               |
|--------------|---------------------|
| Name         | `diaBrand`          |
| Description  | *(none)*            |
| Model Source | `GT_BRANDS`         |
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
modeloPageProj.setProperty("/BRAND", entry.VALUE);
```

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

> **No backend AJAX** — identical to `diaGeography`. Both `search` and `liveChange` are client-side only.

---

## 3. Model Dependencies

| Model            | Field   | Usage                                        |
|------------------|---------|----------------------------------------------|
| `GT_BRANDS`      | `TEXT`  | List display; filter path (Contains)         |
| `GT_BRANDS`      | `VALUE` | Filter path (StartsWith); written on confirm |
| `modeloPageProj` | `BRAND` | Written on confirm: `entry.VALUE`            |

---

## 4. Fiori Migration — XML View Fragment

```xml
<SelectDialog
    id="diaBrand"
    confirm=".onDiaBrandConfirm"
    liveChange=".onDiaBrandLiveChange"
    search=".onDiaBrandSearch"
    items="{GT_BRANDS>/}">
    <items>
        <StandardListItem
            title="{GT_BRANDS>TEXT}"
            description="{GT_BRANDS>VALUE}" />
    </items>
</SelectDialog>
```

---

## 5. Fiori Migration — Controller JavaScript

```javascript
_applyBrandFilter: function (oEvent) {
    var sValue   = oEvent.getParameter("value");
    var oBinding = oEvent.getSource().getBinding("items");
    var oFilter  = [];

    if (typeof sValue === "string" && sValue.trim().length > 0) {
        oFilter = new sap.ui.model.Filter({
            filters: [
                new sap.ui.model.Filter({path: "TEXT",  operator: sap.ui.model.FilterOperator.Contains,   value1: sValue}),
                new sap.ui.model.Filter({path: "VALUE", operator: sap.ui.model.FilterOperator.StartsWith, value1: sValue})
            ]
        });
    }
    oBinding.filter([oFilter]);
},

onDiaBrandLiveChange: function (oEvent) { this._applyBrandFilter(oEvent); },
onDiaBrandSearch:     function (oEvent) { this._applyBrandFilter(oEvent); },

onDiaBrandConfirm: function (oEvent) {
    var oEntry = oEvent.getParameter("selectedItem").getBindingContext("GT_BRANDS").getObject();
    this.getView().getModel("modeloPageProj").setProperty("/BRAND", oEntry.VALUE);
},
```

---

## 6. Migration Notes

1. **Structurally identical to `diaGeography`** — same filter strategy, same confirm pattern, same no-AJAX approach. Only differences: model (`GT_BRANDS` vs `GT_GEOGRAPHYLIST`), target property (`BRAND` vs `GEOGRAPHY`), and filter secondary path (`VALUE` in both cases).
2. **No backend AJAX on search** — full brand list is pre-loaded. Client-side filter only.
3. **Dual-path composite OR filter** — TEXT Contains OR VALUE StartsWith.
4. **`oEvent.oSource` → `oEvent.getSource()`** in Fiori.
