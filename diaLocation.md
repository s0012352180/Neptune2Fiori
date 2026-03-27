# diaLocation – Dialog Screen Specification

> **Source PDF:** `Additional_Dialog_screen.pdf` (page 8–9)
> **Dialog Control ID:** `diaLocation`
> **Control Type:** `sap.m.SelectDialog`
> **Description:** *(none)*
> **Purpose:** Location lookup. Writes selected location value to `modeloPageProj.LOCATION`.

---

## 1. Dialog Root — `sap.m.SelectDialog`

| Property     | Value               |
|--------------|---------------------|
| Name         | `diaLocation`       |
| Description  | *(none)*            |
| Model Source | `GT_LOCATIONS`      |
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
modeloPageProj.setProperty("/LOCATION", entry.LOCATION);
```

> **Key difference from `diaGeography` and `diaBrand`:** The confirm writes `entry.LOCATION` (not `entry.VALUE`). The field key in the `GT_LOCATIONS` model is `LOCATION`, not `VALUE`.

---

### 2.2 `liveChange` Event

```javascript
var searchTerm = oEvent.getParameter("value");
var binding = oEvent.oSource.getBinding("items");

var filter = [];
if (typeof searchTerm === "string" && searchTerm.trim().length > 0) {
    filter = new sap.ui.model.Filter({
        filters: [
            new sap.ui.model.Filter({path: "TEXT",     operator: "Contains",   value1: searchTerm}),
            new sap.ui.model.Filter({path: "LOCATION", operator: "StartsWith", value1: searchTerm})
        ]
    });
}
binding.filter([filter]);
```

> Secondary filter path is `LOCATION` (not `VALUE` as in `diaGeography`/`diaBrand`).

---

### 2.3 `search` Event

```javascript
var searchTerm = oEvent.getParameter("value");
var binding = oEvent.oSource.getBinding("items");

var filter = [];
if (typeof searchTerm === "string" && searchTerm.trim().length > 0) {
    filter = new sap.ui.model.Filter({
        filters: [
            new sap.ui.model.Filter({path: "TEXT",     operator: "Contains",   value1: searchTerm}),
            new sap.ui.model.Filter({path: "LOCATION", operator: "StartsWith", value1: searchTerm})
        ]
    });
}
binding.filter([filter]);
```

> **No backend AJAX** — client-side filter only, same as `diaGeography` and `diaBrand`.

---

## 3. Model Dependencies

| Model            | Field      | Usage                                          |
|------------------|------------|------------------------------------------------|
| `GT_LOCATIONS`   | `TEXT`     | List display; filter path (Contains)           |
| `GT_LOCATIONS`   | `LOCATION` | Filter path (StartsWith); written on confirm   |
| `modeloPageProj` | `LOCATION` | Written on confirm: `entry.LOCATION`           |

---

## 4. Fiori Migration — XML View Fragment

```xml
<SelectDialog
    id="diaLocation"
    confirm=".onDiaLocationConfirm"
    liveChange=".onDiaLocationLiveChange"
    search=".onDiaLocationSearch"
    items="{GT_LOCATIONS>/}">
    <items>
        <StandardListItem
            title="{GT_LOCATIONS>TEXT}"
            description="{GT_LOCATIONS>LOCATION}" />
    </items>
</SelectDialog>
```

---

## 5. Fiori Migration — Controller JavaScript

```javascript
_applyLocationFilter: function (oEvent) {
    var sValue   = oEvent.getParameter("value");
    var oBinding = oEvent.getSource().getBinding("items");
    var oFilter  = [];

    if (typeof sValue === "string" && sValue.trim().length > 0) {
        oFilter = new sap.ui.model.Filter({
            filters: [
                new sap.ui.model.Filter({path: "TEXT",     operator: sap.ui.model.FilterOperator.Contains,   value1: sValue}),
                new sap.ui.model.Filter({path: "LOCATION", operator: sap.ui.model.FilterOperator.StartsWith, value1: sValue})
            ]
        });
    }
    oBinding.filter([oFilter]);
},

onDiaLocationLiveChange: function (oEvent) { this._applyLocationFilter(oEvent); },
onDiaLocationSearch:     function (oEvent) { this._applyLocationFilter(oEvent); },

onDiaLocationConfirm: function (oEvent) {
    var oEntry = oEvent.getParameter("selectedItem").getBindingContext("GT_LOCATIONS").getObject();
    this.getView().getModel("modeloPageProj").setProperty("/LOCATION", oEntry.LOCATION);
},
```

---

## 6. Comparison Table — All Search/Lookup SelectDialogs

| Aspect                  | `diaSearchAuthBy`         | `diaSearchOnBehalf`       | `diaSearchCostCenter`     | `diaGeography`        | `diaBrand`            | `diaLocation`         |
|-------------------------|---------------------------|---------------------------|---------------------------|-----------------------|-----------------------|-----------------------|
| Control type            | `sap.m.SelectDialog`      | `sap.m.SelectDialog`      | `sap.m.SelectDialog`      | `sap.m.SelectDialog`  | `sap.m.SelectDialog`  | `sap.m.SelectDialog`  |
| Model Source            | `GT_AUTH_BY_LIST`         | `GT_AGENT_LIST`           | `GT_KOSTL`                | `GT_GEOGRAPHYLIST`    | `GT_BRANDS`           | `GT_LOCATIONS`        |
| Filter field(s)         | `TEXT` only               | `TEXT` only               | `TEXT` only               | `TEXT` + `VALUE`      | `TEXT` + `VALUE`      | `TEXT` + `LOCATION`   |
| Backend AJAX on search  | Yes — `GetAuthorizedBy`   | Yes — `GetOnBehalfPerson` | Yes — `GetCostCenter`     | **No**                | **No**                | **No**                |
| setBusy on search       | Yes                       | Yes                       | Yes                       | No                    | No                    | No                    |
| liveChange backend call | No                        | No                        | No                        | No                    | No                    | No                    |
| Confirm writes to       | `inpAuthBy` + model       | `userField` (dynamic)     | `inpCostCenter` or multi  | model only            | model only            | model only            |
| Model field written     | `AUTH_BY_ID` (OBJID)      | `userField` (OBJID)       | `KOSTL` (VALUE)           | `GEOGRAPHY` (VALUE)   | `BRAND` (VALUE)       | `LOCATION` (LOCATION) |
| fireChange on confirm   | Yes                       | Yes                       | Yes                       | No                    | No                    | No                    |
| Update AJAX on confirm  | Yes — `UpdateAuthorizedBy`| No                        | No                        | No                    | No                    | No                    |
| Dual-path filter        | No                        | No                        | No                        | Yes                   | Yes                   | Yes                   |

---

## 7. Migration Notes

1. **`LOCATION` field key — not `VALUE`** — The `GT_LOCATIONS` model uses `LOCATION` as the key field. Both the filter secondary path and the confirm write use `entry.LOCATION`. Do not substitute `entry.VALUE`.
2. **Client-side only** — No backend AJAX on search or liveChange. The full locations list is pre-loaded into `GT_LOCATIONS`.
3. **`oEvent.oSource` → `oEvent.getSource()`** in Fiori.
4. **Structurally identical to `diaBrand`/`diaGeography`** except for model name and key field name. The shared `_applyLocationFilter` helper pattern avoids code duplication.
