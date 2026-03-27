# Master Scaffold Spec: `ZDCI_NAD_PROJECT_REQUEST`
## Neptune → SAP Fiori Freestyle Migration
### Top-Level Layout & Section Registry

---

## 1. PURPOSE OF THIS DOCUMENT

This spec defines the **outermost container hierarchy** of the application — the `ObjectPageLayout`, its `ObjectPageHeader`, and all top-level `ObjectPageSection` entries. It is the skeleton into which all subsection specs slot. Read this first before generating any Fiori XML view code.

---

## 2. FULL CONTROL TREE — TOP LEVEL

```
oShell  [sap.m.Shell]
  └── oApp  [sap.m.App]
        ├── oBusyIndicator  [sap.m.BusyIndicator]
        └── oPageProj  [sap.m.Page]
              ├── headerPageProj  [page header]
              └── oplForm  [sap.uxap.ObjectPageLayout]
                    ├── ophProj  [sap.uxap.ObjectPageHeader]
                    ├── hcAttributes  [ObjectPageHeaderContent]
                    │     ├── vlAttributesBlock1
                    │     └── vlAttributesBlock2
                    └── sections:
                          ├── sectionProj          [sap.uxap.ObjectPageSection]
                          ├── sectionApproval      [sap.uxap.ObjectPageSection]
                          ├── sectionAttachments   [sap.uxap.ObjectPageSection]
                          ├── sectionApprovals     [sap.uxap.ObjectPageSection]
                          └── sectionComments      [sap.uxap.ObjectPageSection]
```

---

## 3. `oplForm` — ObjectPageLayout

| Attribute | Value |
|---|---|
| name | `oplForm` |
| alwaysShowContentHeader | `true` |
| styleClass | `opl-two-column` |
| subSectionLayout | `TitleOnLeft` |
| useIconTabBar | *(blank — false by default)* |
| usesTwoColumnsForLarge | `true` |

**Fiori XML:**
```xml
<uxap:ObjectPageLayout
    id="oplForm"
    alwaysShowContentHeader="true"
    styleClass="opl-two-column"
    subSectionLayout="TitleOnLeft"
    usesTwoColumnsForLarge="true">

  <uxap:headerTitle>
    <!-- ophProj -->
  </uxap:headerTitle>

  <uxap:headerContent>
    <!-- hcAttributes -->
  </uxap:headerContent>

  <uxap:sections>
    <!-- sectionProj, sectionApproval, sectionAttachments,
         sectionApprovals, sectionComments -->
  </uxap:sections>

</uxap:ObjectPageLayout>
```

---

## 4. `ophProj` — ObjectPageHeader

| Attribute | Value |
|---|---|
| name | `ophProj` |
| objectImageShape | `Circle` |
| objectImageAlt | `sap-icon://it-dimension` |
| objectTitle | `{GS_PROJ_FORM-PROJECTNAME}` |
| isObjectIconAlwaysVisible | `true` |

> The header also contains action buttons `btnPrevOrder`, `btnToggleEdit`, `btnNextOrder` in its `actions` aggregation.

**Fiori XML:**
```xml
<uxap:ObjectPageHeader
    id="ophProj"
    objectImageShape="Circle"
    objectImageAlt="sap-icon://it-dimension"
    objectTitle="{pageProj>PROJECTNAME}"
    isObjectIconAlwaysVisible="true">
  <uxap:actions>
    <m:Button id="btnPrevOrder"  ... />
    <m:Button id="btnToggleEdit" ... />
    <m:Button id="btnNextOrder"  ... />
  </uxap:actions>
</uxap:ObjectPageHeader>
```

---

## 5. TOP-LEVEL SECTION REGISTRY

### 5.1 `sectionProj` — Project Details

| Attribute | Value |
|---|---|
| name | `sectionProj` |
| title | `Project Details` |
| titleLevel | *(blank)* |
| titleUppercase | `false` |
| styleClass | `sfactor-block` |

**SubSections (in order):**

| SubSection ID | Title | Spec File |
|---|---|---|
| `subsectionRequester` | `Requester` | `subsectionRequester_SPEC.md` |
| `subsectionProjdetail` | `Project Details` | `subsectionProjdetail_SPEC.md` + `subsectionProjdetail_sfBudgetProj2_SPEC.md` |
| `subsectionProjAttDownload` | `Important Forms` | `subsectionProjAttDownload_SPEC.md` |
| `subsectionProjMultiCC` | `Additional Company Code` | `subsectionProjMultiCC_SPEC.md` |

---

### 5.2 `sectionApproval` — Workflow

| Attribute | Value |
|---|---|
| name | `sectionApproval` |
| title | `Workflow` |
| titleUppercase | `false` |

**SubSections (in order):**

| SubSection ID | Title | Spec File |
|---|---|---|
| `subsectionFPA` | `FPA` | `sectionApproval_subsectionFPA_SPEC.md` |
| `subsectionRTR` | `RTR` | `sectionApproval_subsectionRTR_SPEC.md` |
| `subsectionBPC` | `BPC` | `sectionApproval_subsectionBPC_SPEC.md` |

---

### 5.3 `sectionAttachments` — Attachments

| Attribute | Value |
|---|---|
| name | `sectionAttachments` |
| title | `Attachments` |
| Model Source | `GS_ATTACHMENT` |

> ⚠️ `sectionAttachments` has a Model Source of `GS_ATTACHMENT` set directly on the section — unusual. This drives the attachments list table inside `subsectionAttachments`.

**SubSections:** `subsectionAttachments` (spec pending)

---

### 5.4 `sectionApprovals` — Approvals

| Attribute | Value |
|---|---|
| name | `sectionApprovals` |
| title | `Approvals` |
| titleUppercase | `false` |

**SubSections:** `subsectionApprovals` (spec pending)

---

### 5.5 `sectionComments` — Comments

| Attribute | Value |
|---|---|
| name | `sectionComments` |
| title | *(not visible in PDF — likely `Comments`)* |

**SubSections:** `subsectionComments` (spec pending)

---

## 6. SECTION ORDER & VISIBILITY SUMMARY

| # | Section ID | Title | Always Visible | Notes |
|---|---|---|---|---|
| 1 | `sectionProj` | `Project Details` | Yes | Core project data entry |
| 2 | `sectionApproval` | `Workflow` | Conditional | Subsections shown by `PROJ_FLGRP_*` flags |
| 3 | `sectionAttachments` | `Attachments` | Conditional | `CAN_ATTACH` flag |
| 4 | `sectionApprovals` | `Approvals` | Conditional | Approver table |
| 5 | `sectionComments` | `Comments` | Conditional | Comments / history |

---

## 7. MASTER SPEC FILE INDEX

All spec files produced to date, in dependency order:

| File | Component | Lines |
|---|---|---|
| `subsectionRequester_SPEC.md` | `sectionProj → subsectionRequester` | 848 |
| `subsectionProjdetail_SPEC.md` | `sectionProj → subsectionProjdetail → sfBudgetProj` | 1,574 |
| `subsectionProjdetail_sfBudgetProj2_SPEC.md` | `sectionProj → subsectionProjdetail → sfBudgetProj2` | 889 |
| `subsectionProjMultiCC_SPEC.md` | `sectionProj → subsectionProjMultiCC` | 532 |
| `subsectionProjAttDownload_SPEC.md` | `sectionProj → subsectionProjAttDownload` | 510 |
| `sectionApproval_subsectionFPA_SPEC.md` | `sectionApproval → subsectionFPA` | 500 |
| `sectionApproval_subsectionRTR_SPEC.md` | `sectionApproval → subsectionRTR` | 638 |
| `sectionApproval_subsectionBPC_SPEC.md` | `sectionApproval → subsectionBPC` | 324 |
| *(this file)* | Master scaffold | — |

**Pending specs:**
- `sectionAttachments` (attachments list table + upload dialog)
- `sectionApprovals` (approver workflow table)
- `sectionComments` (comments text area)
- `footerPageCCreate` (footer bar with all action buttons)
- `oPageDialogs` (all 15+ dialogs)
- `hcAttributes` (header content blocks)
- `headerPageProj` (page-level header with nav back + title)

---

## 8. COMPLETE FIORI VIEW SKELETON

The full view XML skeleton, with all sections stubbed:

```xml
<mvc:View
    xmlns:mvc="sap.ui.core.mvc"
    xmlns="sap.m"
    xmlns:uxap="sap.uxap"
    xmlns:form="sap.ui.layout.form"
    xmlns:core="sap.ui.core"
    controllerName="zdci.nad.project.request.controller.Main">

  <!-- Page shell -->
  <Shell id="oShell">
    <App id="oApp" busyIndicatorDelay="0">

      <pages>
        <Page id="oPageProj">

          <!-- Page header (nav back + title) -->
          <customHeader>
            <Bar id="headerPageProj">
              <contentLeft>
                <Button id="btnNavBack" icon="sap-icon://nav-back" visible="false" />
              </contentLeft>
              <contentMiddle>
                <Title id="titlePagePCreate" text="Project Code Request" />
              </contentMiddle>
            </Bar>
          </customHeader>

          <content>
            <!-- ObjectPageLayout -->
            <uxap:ObjectPageLayout
                id="oplForm"
                alwaysShowContentHeader="true"
                styleClass="opl-two-column"
                subSectionLayout="TitleOnLeft"
                usesTwoColumnsForLarge="true">

              <!-- HEADER TITLE -->
              <uxap:headerTitle>
                <uxap:ObjectPageHeader
                    id="ophProj"
                    objectImageShape="Circle"
                    objectImageAlt="sap-icon://it-dimension"
                    objectTitle="{pageProj>PROJECTNAME}"
                    isObjectIconAlwaysVisible="true">
                  <uxap:actions>
                    <Button id="btnPrevOrder"  text="Previous" press=".onPrevOrder" />
                    <Button id="btnToggleEdit" text="Edit"     press=".onToggleEdit" />
                    <Button id="btnNextOrder"  text="Next"     press=".onNextOrder" />
                  </uxap:actions>
                </uxap:ObjectPageHeader>
              </uxap:headerTitle>

              <!-- HEADER CONTENT (Attributes) -->
              <uxap:headerContent>
                <!-- hcAttributes — vlAttributesBlock1 + vlAttributesBlock2 -->
                <!-- See hcAttributes spec (pending) -->
              </uxap:headerContent>

              <uxap:sections>

                <!-- SECTION 1: Project Details -->
                <uxap:ObjectPageSection
                    id="sectionProj"
                    title="Project Details"
                    titleUppercase="false"
                    styleClass="sfactor-block">
                  <uxap:subSections>
                    <!-- subsectionRequester -->
                    <!-- subsectionProjdetail -->
                    <!-- subsectionProjAttDownload -->
                    <!-- subsectionProjMultiCC -->
                  </uxap:subSections>
                </uxap:ObjectPageSection>

                <!-- SECTION 2: Workflow (Approval) -->
                <uxap:ObjectPageSection
                    id="sectionApproval"
                    title="Workflow"
                    titleUppercase="false">
                  <uxap:subSections>
                    <!-- subsectionFPA -->
                    <!-- subsectionRTR -->
                    <!-- subsectionBPC -->
                  </uxap:subSections>
                </uxap:ObjectPageSection>

                <!-- SECTION 3: Attachments -->
                <uxap:ObjectPageSection
                    id="sectionAttachments"
                    title="Attachments">
                  <uxap:subSections>
                    <!-- subsectionAttachments (pending spec) -->
                  </uxap:subSections>
                </uxap:ObjectPageSection>

                <!-- SECTION 4: Approvals table -->
                <uxap:ObjectPageSection
                    id="sectionApprovals"
                    title="Approvals"
                    titleUppercase="false">
                  <uxap:subSections>
                    <!-- subsectionApprovals (pending spec) -->
                  </uxap:subSections>
                </uxap:ObjectPageSection>

                <!-- SECTION 5: Comments -->
                <uxap:ObjectPageSection
                    id="sectionComments"
                    title="Comments">
                  <uxap:subSections>
                    <!-- subsectionComments (pending spec) -->
                  </uxap:subSections>
                </uxap:ObjectPageSection>

              </uxap:sections>

            </uxap:ObjectPageLayout>
          </content>

          <!-- Footer bar with action buttons -->
          <footer>
            <Bar id="footerPageCCreate">
              <contentRight>
                <Button id="butSave"           text="Save"            press=".onSave" />
                <Button id="butDelete"         text="Delete"          press=".onDelete" />
                <Button id="butCreate"         text="Create"          press=".onCreate" />
                <Button id="butChange"         text="Change"          press=".onChange" />
                <Button id="butSubmitWthErr"   text="Submit w/ Error" press=".onSubmitWithError" />
                <Button id="butSubmit"         text="Submit"          press=".onSubmit" />
                <Button id="butReject"         text="Reject"          press=".onReject" />
                <Button id="butClose"          text="Close"           press=".onClose" />
                <Button id="butResubmit"       text="Resubmit"        press=".onResubmit" />
              </contentRight>
            </Bar>
          </footer>

        </Page>
      </pages>
    </App>
  </Shell>

  <!-- All Dialogs (oPageDialogs — pending spec) -->

</mvc:View>
```

---

## 9. KEY ARCHITECTURAL NOTES FOR FIORI BUILD

### 9.1 Model naming convention

| Neptune Model | Fiori Named Model | Scope |
|---|---|---|
| `modeloPageProj` / `GS_PROJ_FORM` | `pageProj` | Main form JSON model |
| `modelUrlParameters` / `UrlParameters` | `urlParameters` | URL + runtime parameters |
| `modelvlAttributesBlock1` | `attributesBlock1` | Header attribute block 1 |
| `modelcntr_formdetails` | `cntr_formdetails` | Form step/state control |
| `modeloTableINITIA_GRP` | `tableINITIA_GRP` | FPA initiative group data |
| `DS_PROJ_FORM` | `dsProj` *(or fold into `pageProj`)* | Mode flags per subsection |
| `DT_ADDITIONAL_CCODE` | `additionalCCode` | Multi-CC table model |
| `GT_ORDERDETAILS` | `orderDetails` | RTR order results table |
| `listAttachments` | `attachments` | Attachments list model |
| `AttachmentCategories` | `attachmentCategories` | Static attachment category data |
| `AttachmentsRequired` | `attachmentsRequired` | Static required attachments per type |

### 9.2 CSS classes used across the app

| Class | Purpose |
|---|---|
| `opl-two-column` | ObjectPageLayout two-column layout |
| `sfactor-block` | Section styling on sectionProj |
| `one-line-form-field` | Constrains Input/DatePicker to single-line width |
| `busy-border` | Table border style (tabMultiCC) |
| `error-switch` | Added to Switch on validation error |

### 9.3 Init sequence (from Init.txt)

```
1. sap.ui.getCore().attachInit fires
2. Load discoveryFiori.js from Neptune shared library
3. Create dc (ChildComponent), dirty (DirtyHandler), fu (FileUploader)
4. discoveryFiori.addExpandButtons(oplForm)
5. loadCSS(app.css)
6. oApp.setBusyIndicatorDelay(0)
7. setTimeout(200ms) → addDirtyEvents()
8. If standalone (not child component):
   a. btnNavBack.setVisible(false)
   b. setTimeout(1000ms) → getUrlParameters() + getOnlineGetDetail()
```

In Fiori, steps 1–6 map to `onInit()` in the controller. Steps 7–8 map to a post-init hook or `onAfterRendering()`.

### 9.4 AJAX call naming convention

| Neptune Function | Fiori Private Method |
|---|---|
| `getOnlineGetDetail()` | `_getOnlineGetDetail()` |
| `getOnlineUpdate_Region()` | `_getOnlineUpdateRegion()` |
| `getOnlineUpdateOnbehalf_ID(v)` | `_getOnlineUpdateOnbehalfID(sValue)` |
| `getOnlineGetCostCenter(filter)` | `_getOnlineGetCostCenter(sFilter)` |

### 9.5 Pre-submit validation call chain

The full submit validation sequence (in order):

```javascript
onSubmit: function () {
    var bError = false;
    bError = this._resetFields() || bError;           // clear all ValueStates first
    bError = this._validateRequiredAll() || bError;   // required field check
    bError = this._validateDate() || bError;          // date range check
    bError = this._validateBussJustName() || bError;  // buss just ≠ proj desc
    bError = this._validateAmount() || bError;        // CAPEX/ITL amounts
    bError = this._validateAttachment() || bError;    // attachment completeness
    bError = this._validateApprovalSwitches() || bError; // FPA/RTR/BPC switches
    if (bError) return;
    // Proceed with submit AJAX call
}
```
