# FIS content (schema and conventions)

This directory holds the legal content as data. See the root `README.md` for the repo
overview and the chain structure.

## Adding a new amendment (5 steps)

1. Create `instruments/<gn-id>/instrument.json`: `kind: "amendment-regulations"`,
   `chain`, `amends`, `published` (only if verified), `commencement: null` unless
   stated, and an `events[]` array — one event per amendment act, with `target`
   pointing at the provision being changed and `replacementProvisionId` pointing at
   the new provision (added in step 2).
2. Add the replacement provisions to `instruments/<gn-id>/provisions.json` with ids
   like `schedule-8-of-<gn-id>`. Include a `verification` object; use `text: null` +
   a note if the text is not yet obtained.
3. Update the previous holder of the replaced schedule: set `supersededBy` on the old
   instrument if it is now superseded.
4. Add a source entry in `sources.json` (`parent` = the source it amends,
   `instrumentId` = the id).
5. Run the build and tests: `node --test tests/ && node build/build.js`. The
   validation flags unresolved event targets, missing replacement provisions,
   out-of-order chain dates and unknown card references; warnings flag
   not-yet-extracted texts and unverified rate tables.

## Verification statuses

| status | meaning |
|---|---|
| `verified-against-official-pdf` | text retrieved from an official publisher copy |
| `unverified-transcript` | cleaned OCR of a scan; check against the printed Gazette |
| `unverified-degraded-ocr` | low-quality OCR; many figures/words may be wrong |
| `unverified-from-pwa` | carried from the PWA v0.6 data block; official text not located online |
| `unverified` | text not located at all (placeholder provisions.json) |

## Known gaps / verification checklist

- Schedules 22-26 of GN 153/2004: missing from the scan, `text: null`. Obtain the
  printed Gazette.
- Schedule 27: partial fragment only.
- GN 132/2025: official text not located online; rate tables carried from PWA v0.6
  (`unverified-from-pwa`). Verify every figure against the printed Gazette.
- GN 1132/2011: degraded OCR; re-verify against a clean Gazette copy (note the
  1132 vs 432 header discrepancy in the source scan).
- GN 324/2015, GN 417/2019, GN 657/2019, GN 255/2023: texts not located online;
  provisions.json are placeholders; events are recorded but their targets cannot yet
  be resolved (build warns).
- GN 59/2022: tail of ITEM XI (flowers) truncated in the web retrieval.
- a40: referenced by PWA v0.6 cards a32/a47 but never created; reference
  removed in the repo (no card content may be invented).
