# GMS Field Monitor

A single-file web form for filling the OneGMS **Field Site Monitoring (FSM) report template** in the field, and regenerating the exact Excel file for upload back to OneGMS.

Everything is in [index.html](index.html) — no server, no install, no data leaves the device.

## How it works

1. Export the monitoring template for your project from OneGMS (`Monitoring-<project>_<date>.xlsx`).
2. Open `index.html` in any modern browser (double-click the file, or visit the hosted page once — it keeps working offline afterwards).
3. Drop the exported .xlsx onto the page. The form is built **from the file itself**:
   - fields are located through the `fld_*` named ranges GMS uses to read the upload;
   - dropdown options come from the template's own data-validation lists;
   - project info, indicators and activities are pre-filled from the GMS export.
4. Fill the form. Each section is a short step-by-step wizard (numbered dots = sub-sections; green = complete). Fields marked __*__ are mandatory — you cannot move to the next tab until the current tabs' mandatory fields are complete (tab badges show how many remain). A draft autosaves in the browser after every keystroke; you can also **Export draft** to a JSON file and **Import** it on another machine.
5. Click **Generate Excel for GMS**. The download is the *original* workbook — byte-identical except for your answers injected into the GMS-named cells — so OneGMS accepts it exactly like a hand-filled template.
6. Open the file once in Excel (scores on the `Rep Templ.Scoring` sheet recalculate automatically on open), check it, upload to OneGMS.

Because the form adapts to whatever file is dropped in, it works for any project and any CBPF country export that uses the standard FSM template, and re-loading an already-filled report restores its values for editing.

## Why not just edit the Excel?

The GMS template is sheet-protected, slow on small screens, and easy to corrupt with generic tools (openpyxl-style round-trips silently strip workbook internals). This app never rebuilds the workbook — it patches only the value of edited cells inside the original zip, preserving styles, protection, validations, named ranges, printer settings and metadata untouched.

## Hosting on GitHub Pages (optional)

```bash
git init && git add index.html README.md && git commit -m "GMS field monitoring form"
gh repo create gms-field-monitor --public --source . --push
gh api repos/{owner}/gms-field-monitor/pages -X POST -f build_type=workflow # or enable Pages from branch in repo Settings
```

Then share `https://<owner>.github.io/gms-field-monitor/`. The monitoring template itself should **not** be committed — monitors load their own project export at use time, so no project data is ever published.

## Branding

Styled with the official OCHA brand palette (UN Blue `#009edb` primary, accessible AA/AAA text blues, neutral greys; flat design, no gradients) and the OCHA primary typeface **Roboto** (loaded from Google Fonts when online; falls back to Arial offline, per the brand guideline). The header shows the ESAHF wordmark from `assets/ESAHF_2024_wordmark_Blue.svg` (swap the file to rebrand for another fund). The landing page explains how it works and how it compares with printed forms or filling the Excel directly, illustrated with [OCHA Humanitarian Icons](https://un-ocha.github.io/humanitarian-icons/) stored under `assets/icons/`.

The mandatory-field list is the `REQUIRED` set near the top of the script in `index.html` — add or remove `fld_*` names there to change what is enforced.

## Notes / limits

- Tested against the FSM template family with `fld_*` named ranges (200 fields) — the app warns if a workbook without them is loaded.
- Text answers are written as inline strings (standard OOXML); Excel normalises them on first save.
- Browser requirement: any 2023+ Chrome/Edge/Firefox/Safari (uses native `CompressionStream`).
- Drafts live in the browser's localStorage keyed by file name; "Discard draft" reverts to the file's contents.
