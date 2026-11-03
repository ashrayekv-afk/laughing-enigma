# NKPI 9.1.5.3 — extraction upgrade

Release: **9.1.5.3-extraction-upgrade**

This updates the working 9.1.5.2 OCR-repair release. It preserves the purple bilateral
calculator, natural-eye viewer, frozen models, source confirmation, and recovery calculator.
It is an extraction/scheduling update, not a model refit or clinical validation.

## Upload to the existing repository

1. Extract **NKPI_v9153_Extraction_Upgrade_GitHub.zip** once.
2. Replace these four files at the repository root (for the current deployment,
   use `fictional-waffle`):

       kcn_dual_calculator_app.py
       nkpi_runtime.zip
       requirements.txt
       README.md

   **Keep nkpi_runtime.zip zipped. Replace it and the Python launcher together.**
   Do not put the files inside a new enclosing folder. No new repository is needed.
3. The optional supplied `.streamlit/config.toml` retains the theme/settings. Keep
   it inside `.streamlit/`, not at the root. The OCR changes do not depend on that
   optional file. Existing root-level config.toml is not used as that folder's config.
4. Main file: `kcn_dual_calculator_app.py`. Deployment target stays Python **3.12**.
   Reboot the app through Manage app > overflow menu > Reboot app, then refresh.
5. Check for **Extraction upgrade 9.1.5.3** above the interface and
   **9.1.5.3-extraction-upgrade** in the importer. Run the existing no-patient OCR
   self-check before a de-identified trial import. Verify every result against its source.

Official reboot instructions:
https://docs.streamlit.io/deploy/streamlit-community-cloud/manage-your-app/reboot-your-app

## What changed

- A bounded two-worker pool now processes independent studies concurrently. For
  longitudinal imports, both archives share that same pool; workers are not multiplied.
  Whole-study leases and atomic parameter/read operations prevent cross-eye collisions.
- Scalar measurement review is available before associated map/shape preparation
  finishes. The screen explicitly reports which stage is running. Map preparation
  does not rebuild input rows or overwrite manual edits. Late maps retain the same
  source identity; changed sources/inputs still fail the existing binding checks.
- Label-bound full-number crops improve handling of moved/scaled supported panels.
  Raw ARC/PRC, thinnest pachymetry, final D, and maximum-value labels are checked;
  adjacent values, conflicting reads, incompatible explicit units and ambiguous labels
  remain review cases. Decimal points/signs are retained. Label locations alone are
  not accepted as verified numerical inputs.
- Existing primary verification passes and conditional secondary-reader checks remain.
  No speed-based early acceptance or lower-resolution substitution was introduced.
- Only the versioned static English OCR traineddata may persist in browser storage.
  Unsupported storage falls back to uncached startup. Patient images, extracted text,
  measurement values and report crops are not added to persistent storage.
- Repeated page decoding uses a bounded, import-local memory cache without resizing
  source pixels. It is cleared on reset/completion. OCR results are not cached as new
  independent evidence.
- Changing/clearing source files invalidates previous work and results. Source
  reassociation cancels old map work; stale preview URLs are cleaned up.
- Local timing diagnostics separate startup/discovery/measurements/maps. The download
  contains elapsed times and counts, not names, filenames, dates, pixels or values.

## What to expect

After extraction, **Measurements ready** means you can review and confirm the values
while the associated maps continue preparing in the open browser tab. The viewer may
initially show pending/reference content until its source maps are ready. Map-only
failures do not erase the measurements. Do not treat pending/reference geometry as a
measured patient surface. Source confirmation remains required before calculation.

The improvement is less waiting before review and concurrent independent work. No
clinic-wide speed multiplier or clinical field-accuracy percentage is established.
Extra checking of difficult labels may take additional time and can increase the
number of fields needing manual review rather than guess an answer.

## Verification

47 Python tests; 26 JavaScript test scripts; 12 browser checks passed. The real bundled
primary/secondary OCR engines read synthetic images. Browser checks also covered
label-based crops, ordered parallel extraction, delayed-map review, preserved manual
edits, cancellation, map restart, and longitudinal eye/date isolation. The workflow
checks use explicitly controlled study/map fixtures, not complete real report OCR.

All 200 synthetic eye results (100 baseline, 100 longitudinal) matched the previous
release exactly after excluding the UI version. All three model files, all 13 corneal
modules, all 17 vendored OCR assets/models, map mathematics, and regional binding code
are byte-for-byte unchanged. Predictions, explanations, evidence and audits were
compared with PDF generation disabled.

No live Streamlit deployment, clinic-managed browser/network, or held-out real
Pentacam accuracy/speed benchmark was tested. Browser navigation was blocked here;
real browser-engine tests used local intercepted static assets and a fixture document,
not a deployed Streamlit connection. Python tests ran under 3.13; deployment target is
unchanged at 3.12. Clinical accuracy and speed must be checked on de-identified,
source-verified reports. Detailed results: `docs/EXTRACTION_UPGRADE.md` inside runtime.

## Preserved limitations

No new proprietary export/text-layer parser was added. Existing documented numerical
imports remain supported. Whole-report template registration for every Pentacam layout
and adaptive reductions of OCR passes remain deferred pending report-level validation.
Model predictions and reconstructed report-image anatomy retain their original limits.
NKPI is not a literal future-progression probability or an autonomous treatment decision.
Use approved institutional hosting and de-identified inputs for unapproved deployments.
Never commit patient exports, photos or secrets to this public source repository.
