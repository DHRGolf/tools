# DHR Price Quote Checker

A browser-based tool for reviewing price quotes before they go out for final approval. Upload a quote PDF, run the check, and get a structured report of issues to fix — organized by severity and type.

---

## What it does

The tool reads a DHR price quote PDF and runs it through four tiers of automated checks using the Anthropic Claude API. It flags issues, suggests fixes, and generates a submittal list for job turnover — all before the quote reaches the approver.

Each finding can be clicked to mark it resolved as corrections are made, giving estimators a working checklist rather than a static report.

---

## How to use it

1. Navigate to the tool URL
2. Enter your API key on first visit (stored locally in your browser — reach out to your administrator for a key)
3. Upload the price quote PDF
4. Optionally toggle on Tier 4 and upload a source document (client email, site visit notes)
5. Click **Run Check**
6. Review flagged issues, click each one to mark it resolved as you fix it
7. Download or print the report for reference

---

## Check tiers

### Tier 1 — Template Hygiene *(High confidence)*
Catches mechanical errors that should always be fixed before a quote goes out:

- Unfilled placeholder text (`x-x weeks`, `XXX`, `[Add note here]`, `BAD DESCRIPTION`)
- Missing required fields: estimated duration, wage basis selection, valid-until date
- Spelling and grammar errors in scope text
- Undefined abbreviations in location descriptions (e.g. `GS` without definition)
- Alternates checkboxes (A/B/C) present in the acceptance block when no alternates exist in the quote
- "Owner will supply" language in scope body — should reference the Materials Responsibility table instead
- Dates not formatted in US convention (MM/DD/YYYY)
- Spanish language text anywhere in the document
- Number formatting not following US conventions (period as decimal separator, comma as thousands separator)
- Document formatted for A4 paper instead of US letter (8.5" × 11")

### Tier 2 — Internal Consistency *(Medium-High confidence)*
Checks that different parts of the document agree with each other:

- Project description in the scope section matches the Project field in the header
- Section header names in the scope match line item names in the itemized quote
- Scope sections and line items appear in the same sequence
- Quantities mentioned in scope text match corresponding line item quantities
- Units are consistent between scope and line items (SF vs LF vs EA vs LS)
- The same element is called the same thing throughout the document
- Material specs in the scope are consistent with the specs section
- If drawings/specifications are referenced, the scope cites them but does not repeat their technical detail
- Every material mentioned in the scope has specifications defined somewhere in the document (type, grade, dimensions, manufacturer as applicable)

### Tier 3 — Structural Completeness *(Medium confidence)*
Checks that the document is structurally complete:

- Every line item section in the itemized quote has a corresponding section in the scope of work
- Every scope section has a clear header
- Non-material standalone line items (drain sump, disposal, irrigation modifications) are visually identifiable in the scope — not buried inside another section's bullets
- Multi-feature projects have an overall description before section headers

### Tier 4 — Source Document Cross-Reference *(Requires source upload)*
Checks the quote against client-provided source material:

- Scope covers all features mentioned in the source document
- Site conditions referenced in the source document are acknowledged in the scope or project notes

---

## Submittal List

In addition to the issue report, the tool automatically generates a submittal list from all specified materials in the quote. This covers concrete (mix design, PSI, reinforcement), drainage pipe (type and diameter), bunker liner, bunker sand, sod and turf species, rootzone mix, gravel gradation, and any other specified material. Items are flagged if their specification is missing from the quote, since a submittal cannot be generated without one.

The submittal list is intended for use at job turnover.

---

## Authentication

The tool requires an Anthropic API key to call Claude. On first visit, you will be prompted to enter your key. It is saved in your browser's local storage and is never transmitted anywhere except directly to Anthropic's API (`api.anthropic.com`). If your key becomes invalid, you will be automatically redirected to re-enter it. The **API Key** link in the top-right corner of the tool allows you to update your key at any time.

---

## Technical details

- **Single-file HTML application** — no build process, no dependencies, no backend
- **Model:** `claude-sonnet-4-6` via the Anthropic Messages API
- **PDF reading:** Browser-native `FileReader` API converts the uploaded PDF to base64, which is passed directly to Claude as a document input
- **Authentication:** API key stored in `localStorage`, scoped to this domain
- **No data storage:** Uploaded files and check results exist only in the browser session and are never stored or transmitted beyond the Anthropic API call
- **Drag and drop** supported on both upload zones
- **Print and download:** Reports can be printed or downloaded as a standalone HTML file

---

## Files

| File | Description |
|---|---|
| `index.html` | The complete application — all HTML, CSS, and JavaScript in a single file |
| `README.md` | This file |

---

## Updating the tool

All logic lives in `index.html`. To update check rules, modify the `systemPrompt` variable in the JavaScript section. To deploy an update, replace `index.html` in this repository — GitHub Pages will redeploy automatically within ~60 seconds.

The version number is displayed in the header (`PRE-SUBMISSION REVIEW · Vx.x`) and should be incremented with each meaningful update to make it easy to confirm deployments have propagated.
