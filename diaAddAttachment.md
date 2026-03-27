# diaAddAttachment – Dialog Screen Specification

> **Source PDF:** `diaAddAttachment_dialog.pdf`  
> **Source Code:** `Utilities.txt` — `fileUploadHandler()` function  
> **Source Init:** `Init.txt` — `fu = new discoveryFiori.FileUploader(...)` initialization  
> **Dialog Control ID:** `diaAddAttachment`  
> **Purpose:** File upload dialog. Hosts a `sap.ui.unified.FileUploader` control. On file selection, reads the file as base64, adds it to the `listAttachments` model, closes the dialog, and scrolls the page to the Attachments section.

---

## 1. Dialog Root — `sap.m.Dialog`

| Property          | Value                    |
|-------------------|--------------------------|
| Name              | `diaAddAttachment`       |
| Description       | *(none)*                 |
| Model Source      | *(none set)*             |
| Model Path        | *(empty)*                |
| Ajax ID           | *(not add to View)*      |
| endButton         | `diaAddAttachmentClose`  |

### 1.1 Associations (from PDF page 1)

| Association  | Value                   |
|--------------|-------------------------|
| `endButton`  | `diaAddAttachmentClose` |

> **No leftButton/rightButton** — uses `endButton` only (single close/cancel button). No confirm/save action — file selection itself triggers the upload logic via the FileUploader's native `change` event.

---

## 2. Child Controls

### 2.1 `oFileUploader` — `sap.ui.unified.FileUploader`

| Property           | Value                    |
|--------------------|--------------------------|
| Name               | `oFileUploader`          |
| Ajax ID            | *(not add to View)*      |

#### Events visible in PDF

| Event               | Handler                   |
|---------------------|---------------------------|
| `afterDialogClose`  | *(not set)*               |
| `afterDialogOpen`   | *(not set)*               |
| `change`            | *(not set directly — handled via native DOM in `fileUploadHandler()`)* |
| `fileAllowed`       | *(not set)*               |
| `fileEmpty`         | *(not set)*               |
| `fileSizeExceeded`  | *(not set)*               |
| `fileRenamed`       | *(not set)*               |
| `typeMismatch`      | *(not set)*               |
| `uploadAborted`     | *(not set)*               |

> **File handling is done via native DOM listener**, not via the SAP FileUploader's `change` event. See `fileUploadHandler()` in Section 4.

---

### 2.2 `diaAddAttachmentClose` — `sap.m.Button`

| Property | Value                      |
|----------|----------------------------|
| Name     | `diaAddAttachmentClose`    |
| icon     | `sap-icon://sys-cancel`    |
| press    | `diaAddAttachment.close();`|

> From PDF page 2: press event value is `diaAddAttachment.close();`

---

## 3. Control Hierarchy Summary

```
diaAddAttachment  (sap.m.Dialog)
│  endButton  : diaAddAttachmentClose
│
├── oFileUploader  (sap.ui.unified.FileUploader)
│     (file handling via native DOM — see fileUploadHandler)
│
└── diaAddAttachmentClose  (sap.m.Button)
      icon  : sap-icon://sys-cancel
      press : diaAddAttachment.close();
```

---

## 4. File Upload Handler — `fileUploadHandler()` (from `Utilities.txt`)

This function is called from `Init.txt` via the `discoveryFiori.FileUploader` wrapper. It wires a native DOM `change` listener to the hidden `<input type="file">` element rendered by the SAP FileUploader.

```javascript
function fileUploadHandler() {
    var fileUpload = document.getElementById(oFileUploader.getDomRef().id + "-fu");

    fileUpload.addEventListener("change", function() {
        var reader = new FileReader();

        reader.onload = function(event) {
            var newAttachment = {
                ICON:      "sap-icon://document",
                ICON_COLOR: "",
                FILE_NAME: fileUpload.files[0].name,
                EXTENSION: fileUpload.files[0].name.split(".").pop(),
                MIME_TYPE: fileUpload.files[0].type,
                FILE_DATA: event.target.result,
                UUID:      "",
            };

            // Strip the data URL prefix (e.g. "data:application/pdf;base64,")
            if (newAttachment.FILE_DATA.substring(0, 4) === "data") {
                newAttachment.FILE_DATA = newAttachment.FILE_DATA.substring(
                    newAttachment.FILE_DATA.indexOf(',') + 1
                );
            }

            ModelData.Add(listAttachments, newAttachment);   // Add to attachment list model
            oFileUploader.clear();                           // Reset the FileUploader control
            diaAddAttachment.close();                        // Close the dialog

            oplForm.scrollToSection("sectionAttachments");   // Scroll page to attachment section
        };

        reader.readAsDataURL(fileUpload.files[0]);           // Read file as base64
    }, {
        once: true   // Remove listener after first use to prevent double-firing
    });
}
```

---

## 5. Initialization — `Init.txt`

```javascript
fu = new discoveryFiori.FileUploader(listAttachments, oplForm, AttachmentCategories);
```

The `discoveryFiori.FileUploader` wrapper manages category selection and wires the internal `fileUploadHandler()`. The three parameters pass:
- `listAttachments` — the attachment list model
- `oplForm` — the `ObjectPageLayout` (used for `scrollToSection`)
- `AttachmentCategories` — the category list for the attachment category selector inside the dialog

---

## 6. Attachment Data Structure

Each file added to `listAttachments` has this shape:

| Field       | Type   | Source                                       |
|-------------|--------|----------------------------------------------|
| `ICON`      | string | Hardcoded: `"sap-icon://document"`           |
| `ICON_COLOR`| string | Empty string                                 |
| `FILE_NAME` | string | `fileUpload.files[0].name`                   |
| `EXTENSION` | string | Last segment after `.` in file name          |
| `MIME_TYPE` | string | `fileUpload.files[0].type`                   |
| `FILE_DATA` | string | Base64-encoded file content (prefix stripped)|
| `UUID`      | string | Empty string (assigned by backend on save)   |

---

## 7. Display Attachment — `displayAttachment()` (from `Utilities.txt`)

When a user clicks an existing attachment to view it:

```javascript
function displayAttachment(attachment) {
    if (typeof attachment.FILE_DATA !== "undefined" && attachment.FILE_DATA.length > 0) {
        // File is in memory — create blob and open in new tab
        var byteCharacters = atob(attachment.FILE_DATA);
        var byteNumbers    = new Array(byteCharacters.length);
        for (var i = 0; i < byteCharacters.length; i++) {
            byteNumbers[i] = byteCharacters.charCodeAt(i);
        }
        var blob = new Blob([new Uint8Array(byteNumbers)], { type: attachment.MIME_TYPE });

        if (window.navigator && window.navigator.msSaveOrOpenBlob) {
            window.navigator.msSaveOrOpenBlob(blob, attachment.FILE_NAME);  // IE
        } else {
            window.open(URL.createObjectURL(blob), "_blank");
        }
    } else {
        // File not in memory — retrieve from backend by UUID
        var keyId = "form=" + modelvlAttributesBlock1.oData.FORM_GUID + ",att=" + attachment.UUID;
        var url   = "/neptune/zdci_nad_project_request.html?key=attachment&key_id=" + keyId;
        window.open(url, "_blank");
    }
}
```

---

## 8. Model Dependencies

| Model              | Usage                                                    |
|--------------------|----------------------------------------------------------|
| `listAttachments`  | Attachment array — new entries added via `ModelData.Add` |
| `AttachmentCategories` | Category list passed to `discoveryFiori.FileUploader` |
| `modelvlAttributesBlock1` | `FORM_GUID` used in backend attachment URL      |

---

## 9. Fiori Migration — XML View Fragment

```xml
<!-- diaAddAttachment Dialog -->
<Dialog
    id="diaAddAttachment"
    endButton="diaAddAttachmentClose">

    <content>
        <!-- Category selector (rendered by discoveryFiori.FileUploader wrapper) -->
        <Select id="oAttachmentCategorySelect" />

        <!-- File Uploader -->
        <unified:FileUploader
            id="oFileUploader"
            uploadOnChange="false"
            sendXHR="false"
            change=".onFileUploaderChange" />
    </content>

    <endButton>
        <Button
            id="diaAddAttachmentClose"
            icon="sap-icon://sys-cancel"
            press=".onDiaAddAttachmentClose" />
    </endButton>
</Dialog>
```

---

## 10. Fiori Migration — Controller JavaScript

```javascript
onDiaAddAttachmentClose: function () {
    this.byId("diaAddAttachment").close();
},

onFileUploaderChange: function (oEvent) {
    // In Fiori, use the FileUploader's change event directly instead of native DOM listener
    var oFileUploader = this.byId("oFileUploader");
    var oFile = oEvent.getParameter("files") && oEvent.getParameter("files")[0];

    if (!oFile) { return; }

    var oReader = new FileReader();
    oReader.onload = function (evt) {
        var sData = evt.target.result;

        // Strip data URL prefix
        if (sData.substring(0, 4) === "data") {
            sData = sData.substring(sData.indexOf(",") + 1);
        }

        var oNewAttachment = {
            ICON:       "sap-icon://document",
            ICON_COLOR: "",
            FILE_NAME:  oFile.name,
            EXTENSION:  oFile.name.split(".").pop(),
            MIME_TYPE:  oFile.type,
            FILE_DATA:  sData,
            UUID:       ""
        };

        // Add to attachments model
        var oModel = this.getView().getModel("listAttachments");
        var aItems = oModel.getProperty("/") || [];
        aItems.push(oNewAttachment);
        oModel.setProperty("/", aItems);

        // Reset uploader and close dialog
        oFileUploader.clear();
        this.byId("diaAddAttachment").close();

        // Scroll to attachments section
        this.byId("oplForm").scrollToSection(this.byId("sectionAttachments").getId());
    }.bind(this);

    oReader.readAsDataURL(oFile);
},

onDisplayAttachment: function (oAttachment) {
    if (oAttachment.FILE_DATA && oAttachment.FILE_DATA.length > 0) {
        var sBinary = atob(oAttachment.FILE_DATA);
        var aBytes  = new Uint8Array(sBinary.length);
        for (var i = 0; i < sBinary.length; i++) { aBytes[i] = sBinary.charCodeAt(i); }
        var oBlob   = new Blob([aBytes], { type: oAttachment.MIME_TYPE });
        window.open(URL.createObjectURL(oBlob), "_blank");
    } else {
        var sKeyId = "form=" + this.getView().getModel("modelvlAttributesBlock1").getProperty("/FORM_GUID")
                   + ",att=" + oAttachment.UUID;
        window.open("/neptune/zdci_nad_project_request.html?key=attachment&key_id=" + sKeyId, "_blank");
    }
},
```

---

## 11. Migration Notes

1. **Native DOM listener → SAP event** — Neptune bypasses the SAP FileUploader `change` event and wires directly to the DOM `<input>` element using `document.getElementById(oFileUploader.getDomRef().id + "-fu")`. In Fiori, use the standard `change` event on `sap.ui.unified.FileUploader` — this is cleaner and supported.
2. **`{ once: true }` listener** — Neptune adds the DOM listener with `once: true` to prevent double-firing when the dialog is reopened. In Fiori the SAP `change` event is inherently single-fire per selection, so no equivalent is needed.
3. **`discoveryFiori.FileUploader` wrapper** — In Neptune, the wrapper handles category selection UI and calls `fileUploadHandler()` internally. In Fiori, the category selector and file uploader must be built explicitly in the dialog content.
4. **`ModelData.Add`** — Neptune uses a custom `ModelData.Add()` helper to push to `listAttachments`. In Fiori, read the array from the JSON model, push, and set back.
5. **IE `msSaveOrOpenBlob` path** — The `displayAttachment` IE11 fallback can be removed for modern Fiori deployments.
6. **Backend URL** — The Neptune backend attachment URL `/neptune/zdci_nad_project_request.html?key=attachment&key_id=...` must be replaced with the equivalent SAP Fiori/OData endpoint for attachment retrieval.
7. **`oplForm.scrollToSection("sectionAttachments")`** — In Fiori, `ObjectPageLayout.scrollToSection()` requires the section's control ID, not a string name. Use `this.byId("sectionAttachments").getId()`.
8. **No AJAX on upload** — Attachments are held in memory (`listAttachments`) until the form is saved/submitted. The SAVE or SUBMIT AJAX call is responsible for persisting the base64 data to the backend.
