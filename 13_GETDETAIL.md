# AJAX: GETDETAIL
**App:** ZDCI_NAD_PROJECT_REQUEST
**Purpose:** Loads full form detail when opening an existing record (display/edit mode).

## Parameters
| Direction | Name | Type / Notes |
|-----------|------|--------------|
| INPUT | _(see screenshots — 3 images in source doc)_ | Record key / form ID |
| OUTPUT | PROJ_FLGRP_0001_MOD to 0004_MOD | Section expand state flags |
| OUTPUT | REQUEST_FROM | "N" or blank = hide status icon |
| OUTPUT | PROGRAM_STATE | "E" = show message |
| OUTPUT | PROGRAM_MESSAGE | Message text |

> ⚠️ Full parameter list captured as screenshots — fill in from Neptune designer.

## ajaxSuccess
```js
setUI();
resetFields();
oApp.to(modeloPageProj);
dirty.setDirty(false);
discoveryFiori.addExpandButtons(oplForm);
setTimeout(function() {
    // Scroll to expanded section
    if (modeloPageProj.oData.PROJ_FLGRP_0001_MOD == "Expanded") {
        oplForm.scrollToSection(getControlName("subsectionRequester"));
    } else if (modeloPageProj.oData.PROJ_FLGRP_0002_MOD == "Expanded") {
        oplForm.scrollToSection(getControlName("subsectionFPA"));
    } else if (modeloPageProj.oData.PROJ_FLGRP_0003_MOD == "Expanded") {
        oplForm.scrollToSection(getControlName("subsectionRTR"));
    } else if (modeloPageProj.oData.PROJ_FLGRP_0004_MOD == "Expanded") {
        oplForm.scrollToSection(getControlName("subsectionBPC"));
    }
    // Status icon visibility
    if (modelUrlParameters.oData.MODE === "" ||
        (modeloPageProj.oData.REQUEST_FROM === 'N' ||
         modeloPageProj.oData.REQUEST_FROM === "")) {
        colStatusICON.setVisible(false);
    } else {
        colStatusICON.setVisible(true);
    }
    // Program state message
    switch (modelProgramState.oData.PROGRAM_STATE) {
        case 'E':
            sap.m.MessageToast.show(modelProgramState.oData.PROGRAM_MESSAGE);
            break;
    }
    // Additional helpers
    visible_additional_field();
    set_attachment_visible();
    set_visibility_amount();
    setprocessingregion();
    updateinitgrp();
    oApp.setBusy(false);
}, 500);
```

## ajaxError
```js
oApp.setBusy(false);
```

## Notes
- Most complex success handler in the app — calls 5 helper functions
- Scrolls to whichever form section backend flags as "Expanded"
- Hides/shows `colStatusICON` based on REQUEST_FROM and MODE
- Helper functions called: `setUI()`, `resetFields()`, `visible_additional_field()`,
  `set_attachment_visible()`, `set_visibility_amount()`, `setprocessingregion()`, `updateinitgrp()`
- Called via `getOnlineGetDetail()`
