# diaEmployeeInfo – Dialog Screen Specification

> **Source PDF:** `diaEmployeeInfo.pdf`
> **Dialog Control ID:** `diaEmployeeInfo`
> **Control Type:** `sap.m.ResponsivePopover`
> **Model Source:** `GS_EMPLOYEE_INFO`
> **Purpose:** Display-only popover showing employee information fields populated from the `GS_EMPLOYEE_INFO` structure. No user input — read-only display only.

---

## 1. Dialog Root — `sap.m.ResponsivePopover`

| Property          | Value               |
|-------------------|---------------------|
| Name              | `diaEmployeeInfo`   |
| Description       | *(none)*            |
| Model Source      | *(none set on root)*|
| title             | `Employee Info`     |
| Ajax ID           | *(not add to View)* |

### 1.1 Events

| Event        | Handler        |
|--------------|----------------|
| `afterClose` | *(not set)*    |
| `afterOpen`  | *(not set)*    |
| `beforeClose`| *(not set)*    |
| `beforeOpen` | *(not set)*    |

> **Note:** `sap.m.ResponsivePopover` — not a `sap.m.Dialog`. This renders as a Popover on desktop and a Dialog on mobile. No buttons visible in PDF; it is dismissed by clicking outside (default popover behaviour).

---

## 2. Content Container

### 2.1 `cFormEmployeeInfo` — `sap.ui.layout.form.SimpleForm` (or equivalent form)

The popover contains a single form `cFormEmployeeInfo` with label/text pairs bound to `GS_EMPLOYEE_INFO`.

---

## 3. Child Controls — Label/Text Pairs

Each row is a label (`sap.m.Label` — prefix `lblo`) paired with a text display (`sap.m.Text` — prefix `txto`). All text bindings are read from `GS_EMPLOYEE_INFO`.

| # | Label Control ID              | Text Control ID               | GS_EMPLOYEE_INFO Field (inferred) |
|---|-------------------------------|-------------------------------|-----------------------------------|
| 1 | `lbloFormEmployeeInfoP...`    | `txtoFormEmployeeInfoP...`    | Person / Name (P prefix)          |
| 2 | `lbloFormEmployeeInfoEl...`   | `txtoFormEmployeeInfoEl...`   | Email / Employee ID (El prefix)   |
| 3 | `lbloFormEmployeeInfoEl...`   | `txtoFormEmployeeInfoEl...`   | Email line 2 or Employee line (El)|
| 4 | `lbloFormEmployeeInfoEl...`   | `txtoFormEmployeeInfoEl...`   | Employee detail (El)              |
| 5 | `lbloFormEmployeeInfoC...`    | `txtoFormEmployeeInfoC...`    | Cost Center / Company (C prefix)  |
| 6 | `lbloFormEmployeeInfoC...`    | `txtoFormEmployeeInfoC...`    | Company Code / CC line 2 (C)      |
| 7 | `lbloFormEmployeeInfoPl...`   | *(phone control below)*       | Phone label                       |
| 8 | `oEmpPhone`                   | *(special control)*           | Phone number — `sap.m.Link` or `sap.m.Text` |
| 9 | `lbloFormEmployeeInfoO...`    | `txtoFormEmployeeInfoO...`    | Organization / Office (O prefix)  |

> **Note:** The Neptune tree shows truncated control IDs (names cut off by panel width). Full IDs follow the pattern `lbloFormEmployeeInfo{FieldCode}` / `txtoFormEmployeeInfo{FieldCode}`. The `oEmpPhone` control is distinct — likely a `sap.m.Link` to enable `tel:` dialing. Confirm exact field names from the backend `GS_EMPLOYEE_INFO` ABAP structure.

---

## 4. Model — `GS_EMPLOYEE_INFO` Structure

The popover is bound to `GS_EMPLOYEE_INFO`. Typical fields in an employee info structure for this application context:

| Field (expected) | Description                  |
|------------------|------------------------------|
| `NAME`           | Full name                    |
| `PERNR`          | Personnel number             |
| `EMAIL`          | Email address                |
| `DEPARTMENT`     | Department                   |
| `KOSTL`          | Cost center                  |
| `COMP_CODE`      | Company code                 |
| `PHONE`          | Phone number (`oEmpPhone`)   |
| `ORG_UNIT`       | Org unit / office            |

> Verify exact field names against the ABAP `GS_EMPLOYEE_INFO` structure definition.

---

## 5. Fiori Migration — XML View Fragment

```xml
<!-- diaEmployeeInfo ResponsivePopover -->
<ResponsivePopover
    id="diaEmployeeInfo"
    title="Employee Info"
    placement="Auto">

    <content>
        <form:SimpleForm
            id="cFormEmployeeInfo"
            editable="false"
            layout="ResponsiveGridLayout">

            <form:content>
                <!-- Row 1: Person/Name -->
                <Label id="lbloFormEmployeeInfoName" text="Name" />
                <Text id="txtoFormEmployeeInfoName" text="{GS_EMPLOYEE_INFO>/NAME}" />

                <!-- Row 2: Personnel Number -->
                <Label id="lbloFormEmployeeInfoPernr" text="Personnel No." />
                <Text id="txtoFormEmployeeInfoPernr" text="{GS_EMPLOYEE_INFO>/PERNR}" />

                <!-- Row 3: Email -->
                <Label id="lbloFormEmployeeInfoEmail" text="Email" />
                <Text id="txtoFormEmployeeInfoEmail" text="{GS_EMPLOYEE_INFO>/EMAIL}" />

                <!-- Row 4: Department -->
                <Label id="lbloFormEmployeeInfoDept" text="Department" />
                <Text id="txtoFormEmployeeInfoDept" text="{GS_EMPLOYEE_INFO>/DEPARTMENT}" />

                <!-- Row 5: Cost Center -->
                <Label id="lbloFormEmployeeInfoCC" text="Cost Center" />
                <Text id="txtoFormEmployeeInfoCC" text="{GS_EMPLOYEE_INFO>/KOSTL}" />

                <!-- Row 6: Company Code -->
                <Label id="lbloFormEmployeeInfoCoCode" text="Company Code" />
                <Text id="txtoFormEmployeeInfoCoCode" text="{GS_EMPLOYEE_INFO>/COMP_CODE}" />

                <!-- Row 7: Phone -->
                <Label id="lbloFormEmployeeInfoPhone" text="Phone" />
                <Link id="oEmpPhone"
                    text="{GS_EMPLOYEE_INFO>/PHONE}"
                    href="{= 'tel:' + ${GS_EMPLOYEE_INFO>/PHONE} }" />

                <!-- Row 8: Org Unit -->
                <Label id="lbloFormEmployeeInfoOrg" text="Org Unit" />
                <Text id="txtoFormEmployeeInfoOrg" text="{GS_EMPLOYEE_INFO>/ORG_UNIT}" />
            </form:content>
        </form:SimpleForm>
    </content>
</ResponsivePopover>
```

> **Important:** Field names in bindings (`NAME`, `PERNR`, `EMAIL`, etc.) must be confirmed against the actual `GS_EMPLOYEE_INFO` ABAP structure. The above uses inferred names from the truncated control IDs in the PDF.

---

## 6. Fiori Migration — Controller JavaScript

```javascript
// Open the employee info popover anchored to a control
onOpenEmployeeInfo: function (oEvent) {
    var oPopover = this.byId("diaEmployeeInfo");
    var oSource  = oEvent.getSource();

    // Model is set before opening — populated by AJAX response into GS_EMPLOYEE_INFO
    oPopover.openBy(oSource);
},
```

---

## 7. Migration Notes

1. **`sap.m.ResponsivePopover` not `sap.m.Dialog`** — This is the only popover-style control in the entire dialog inventory. It must use `openBy(oControl)` not `.open()`. The anchor control is whatever button/link triggers the employee info display.
2. **Read-only display only** — No input fields, no buttons, no event handlers. All controls are `sap.m.Text` (or `sap.m.Link` for phone). Do not add edit capability.
3. **`oEmpPhone` is a special control** — Named separately from the label/text pattern, suggesting it is a `sap.m.Link` with a `tel:` href for click-to-call. Confirm from source XML.
4. **Model populated by AJAX** — `GS_EMPLOYEE_INFO` is populated by a backend AJAX call before the popover is opened. The trigger button likely calls `getOnlineGetEmployeeInfo()` or similar, then opens the popover on AJAX success.
5. **Truncated control IDs** — The Neptune Cockpit panel cuts off the full control IDs. The pattern is `lbloFormEmployeeInfo{X}` / `txtoFormEmployeeInfo{X}`. Extract exact IDs from the source XML view file if available.
6. **`title: "Employee Info"`** — Confirmed from the PDF properties panel (`styleClassMargin` row shows `Employee Info` as the title value).
