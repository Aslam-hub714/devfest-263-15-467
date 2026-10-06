# Tender Document Package Builder

## Overview

**Tender Document Package Builder** is a frontend-only, browser-based web application designed to help bidders and procurement personnel prepare, validate, and assemble structured tender submission packages. The entire workflow runs directly inside the client's web browser with zero server-side dependencies or external database storage.

The tool validates document requirements against uploaded PDF files, computes exact cryptographic checksums (SHA-256) to identify duplicate files, verifies document expiry dates against submission deadlines, and merges matched documents into a single downloadable PDF package featuring an official cover page, a sequential Table of Contents (Index page), and uniform footer page numbering.

---

## Problem Solved

Preparing tender submissions is often prone to administrative non-compliance due to:
* Missing mandatory legal, financial, or technical documents.
* Expired trade licenses, tax clearance certificates, or bank guarantees.
* Accidentally uploading duplicate or misplaced files across multiple requirement slots.
* Manual and inconsistent page numbering across disparate donor PDF documents.
* Lack of bilingual accessibility for teams working across English and Bangla (বাংলা).

This application provides instant client-side verification, automatic compliance checking, and streamlined package assembly before final bid submission.

---

## Main Features

### Tender Requirements
* **Requirements Ingestion**: Upload custom `requirements.json` specification files or click **"Demo"** to populate realistic tender criteria.
* **Tender Metadata**: Captures and edits Tender ID, Tender Title, Procuring Entity, Bidder Name, and Submission Deadline.
* **Sample Schema Export**: Download standard `requirements.json` templates directly from the workspace.

### PDF Upload & Validation
* **Multi-PDF Ingestion**: Drag-and-drop or select multiple PDF files simultaneously.
* **File Validation**: Enforces `.pdf` format validation; non-PDF files are rejected with clear user notifications.
* **Page Counting**: Inspects PDF structure client-side to calculate accurate page counts for each uploaded document.
* **File Management**: View file sizes, page counts, and SHA-256 hashes, with individual removal or full clear capabilities.

### Requirement Matching
* **One-to-One Mapping**: Dropdown selection maps each uploaded PDF to its corresponding tender requirement.
* **Slot Exclusivity**: Prevents matching the same file or identical-hash duplicates to multiple requirements.
* **Detachment**: Instant file unmatching with automatic recalculation of package readiness.

### Status & Expiry Validation
* **Dynamic Status Engine**: Real-time compliance calculation for every requirement:
  * **OK (Green)**: Requirement satisfied with a valid, non-expired document.
  * **Missing (Red)**: Mandatory requirement without an attached file (blocks generation).
  * **Expiry date needed (Yellow)**: Requirement with `has_expiry = true` matched to a file without an entered expiry date (blocks generation).
  * **Expired (Red)**: Document expiry date precedes the submission deadline (blocks generation).
  * **Not provided (Gray)**: Optional requirement without an attached file (permitted; excluded from final package).
* **Deadline Day Rule**: Expiry on the exact day of the submission deadline is treated as compliant (**OK**).
* **Instant Reactivity**: Statuses update immediately upon file selection, date change, detachment, or deletion.

### Duplicate Detection
* **Exact Duplicate Detection**: Computes SHA-256 binary hashes for every uploaded PDF using Crypto-JS.
* **Duplicate Alert Banner**: Identical files (matching SHA-256 hashes) display warning badges and are blocked from being matched to requirements.
* **Duplicate Cleanup**: One-click **"Remove Duplicate Copies"** button cleans redundant files while preserving unique originals.

### Package Generation
* **Enforced Validation Gate**: The **"Generate Package"** and **"Submission Export"** buttons remain disabled whenever blocking issues or unresolved duplicate files exist.
* **Official Cover Page (Page 1)**: Formatted in English with Tender ID, Tender Title, Procuring Entity, Bidder Name, Submission Deadline, Generation Timestamp, and an authorized bidder declaration.
* **Document Assembly**: Merges verified donor PDFs in ascending requirement order (`order` 1 to N), omitting optional unmatched requirements.
* **Uniform Page Footers**: Stamps `<tender_id> | Page X of Y` centered at the bottom of every page (where Y includes Cover, Table of Contents, and all attached donor pages).
* **Deterministic File Naming**: Final output is automatically named `<tender_id>_Package.pdf` (e.g., `TND-2026-BD-8902_Package.pdf`).

### Bilingual Support
* **Bilingual UI**: Toggle between English and Bangla (বাংলা) at any time.
* **Bilingual Document Titles**: Requirement titles smoothly toggle between `title_en` and `title_bn` across the entire workspace.
* **Localized Guidance**: System banners, button labels, validation hints, and error alerts are presented in the chosen language.

---

## Bonus Features

1. **Table of Contents / Index Page (Page 2)**:
   * Inserts an official Table of Contents immediately following the Cover Page.
   * Lists each included document, matched file name, page count, and **exact starting page number** in the merged document.
   * Enclosed donor documents begin strictly at **Page 3**, ensuring total offset accuracy.
2. **Digital Seal & Signature Overlay**:
   * Upload custom transparent PNG seal/signature graphics or generate a procedural sample bidder seal with one click.
   * Selectable overlay scope: **All Pages**, **Cover Only**, or **Documents Only**.
   * Stamps the seal image onto designated pages during package compilation.
3. **Audit Checklist Export (`.xlsx`)**:
   * Uses SheetJS to export an Excel workbook named `<tender_id>_Checklist.xlsx`.
   * Includes columns: `Requirement ID`, `Document Title (EN & BN)`, `Matched File Name`, `Page Count`, `Expiry Date`, and `Status`.
4. **Interactive PDF Preview (`pdf.js`)**:
   * Preview eye-icon buttons next to each uploaded file and requirement row.
   * Modal dialog renders page thumbnails with previous/next page navigation, zoom controls, and single-file download.
5. **Corrupt & Password-Protected PDF Handling**:
   * Client-side parsing wrapped in `try-catch` blocks.
   * Corrupt or password-protected files display a red **`Corrupt or Protected PDF`** badge and are safely bypassed during compilation without runtime errors.
6. **50MB Total Size Alert**:
   * Automatically displays a warning toast if total uploaded file volume exceeds 50MB.
7. **Bangla Text Rendering for PDF**:
   * Uses an HTML5 canvas-to-PNG pipeline to embed Bengali document titles into `pdf-lib` without Unicode character corruption or font encoding errors.
8. **Save & Reopen Workspace**:
   * Save progress to browser `localStorage`.
   * Export and import complete session states as `<tender_id>_session.json` files (including base64 file buffers and seal configuration).
9. **Fuzzy Auto-Matching**:
   * Compares uploaded filenames against bilingual requirement titles using string similarity to automatically assign matching files.
10. **Screenshot-Ready Submission View**:
    * Clean toggle mode that hides configuration panels and presents the Document Requirements Table with high-contrast status badges (Green for OK, Red for Missing/Expired, Yellow for Expiry Needed) for evaluation captures.
11. **Submission Folder Pack Export**:
    * Single-click export downloading the finalized package PDF, Excel checklist, and session JSON file in sequence.

---

## Technology Stack

* **Core Framework**: Vue 3 (`v3.3.4` via unpkg CDN) — Reactive state management and computed validation.
* **Styling**: Tailwind CSS (CDN) — Responsive layout and high-contrast status theme.
* **PDF Engine**: pdf-lib (`v1.17.1` via unpkg CDN) — In-browser PDF generation, merging, custom text drawing, and footer stamping.
* **Cryptography**: Crypto-JS (`v4.1.1` via cdnjs) — SHA-256 client-side binary hashing for exact duplicate detection.
* **Spreadsheet Export**: SheetJS / XLSX (`v0.20.1` via cdn.sheetjs.com) — Excel `.xlsx` checklist workbook generation.
* **PDF Rendering**: pdf.js (`v3.11.174` via cdnjs) — Client-side PDF page rendering in preview modal.
* **Fonts**: Google Fonts (*Inter* for UI, *Hind Siliguri* for authentic Bangla typography, *JetBrains Mono* for IDs and hashes).

---

## Architecture

* **100% Client-Side Processing**: All operations (file parsing, SHA-256 hash generation, expiry date comparison, PDF page drawing, and Excel sheet compilation) execute exclusively within the user's browser runtime.
* **No Prohibited Services**: No participant-controlled backend, no serverless functions, and no online databases (Firebase, Supabase, Appwrite, etc.) are used.
* **Browser Storage**: Uses browser `localStorage` solely for user-initiated session saving.
* **Data Privacy**: Uploaded files and metadata never leave the client machine.

---

## Quick Start / How to Run

### Option 1: Direct Browser Launch (Zero-Build)
Because the application is written as a standalone HTML5 application using CDN libraries, you can run it immediately without building:
1. Double-click `index.html` or open it directly in the latest **Google Chrome**.

### Option 2: Run with Static HTTP Server
```bash
# Using Python 3 built-in HTTP server:
python -m http.server 8080

# Or using npx serve:
npx serve .
```
Navigate to `http://localhost:8080` in Google Chrome.

### Option 3: Run with Vite Dev Server (Included Workspace)
```bash
# Install development dependencies:
npm install

# Start local server:
npm run dev
```
Navigate to `http://localhost:3000` in Google Chrome.

---

## Live Demo

* **Live URL**: https://tenderflow46.netlify.app/

*The live deployment is hosted on Netlify as a static frontend website matching the final submission commit.*

---

## Competition Submission Information

* **Name**: Nuhan Aslam
* **Registration Number**: 263-15-467
* **Contest**: DevFest 2026 AI Vibe-Coding Contest

---

## AI Tools Used

* **Google AI Studio Build** (powered by Gemini models) — Prompt-driven frontend engineering, edge-case hardening, and compliance auditing.

---

## Most Useful AI Prompt

> **Prompt 3: Table of Contents (Index page), PDF preview modal, Bangla fallback, and pack exporting**
> 
> *"Now make the final complete updates to the single-file index.html Vue 3 application to complete all core and bonus requirements for competition submission:*
> *1. INDEX PAGE (TABLE OF CONTENTS) AFTER COVER PAGE (BONUS): Generate an 'Index / Table of Contents' page immediately after the Cover Page (Page 2). List each included document title, matched file name, and the EXACT starting page number in the final package. Account for both Cover Page (P.1) and Index Page (P.2).*
> *2. BANGLA TEXT RENDERING ON COVER & INDEX (BONUS): Ensure document titles switch smoothly between English and Bangla (title_bn). For PDF generation, handle fallback gracefully via canvas export so PDF generation never crashes or outputs broken unicode characters.*
> *3. PDF PREVIEW MODAL (pdf.js): Include pdf.js CDN and add a Preview PDF eye icon button next to each uploaded file and requirement row with an interactive modal.*
> *4. AUTOMATIC OUTPUT SAVING: Export Submission Folder Pack button downloading <tender_id>_Package.pdf, <tender_id>_Checklist.xlsx, and <tender_id>_state.json.*
> *5. FINAL UI COMPLIANCE: Real-time header badge ('READY TO SUBMIT' / 'ACTION REQUIRED') and clear visual feedback for rejected non-PDFs and SHA-256 duplicates."*

---

## Known Problems

* **Canvas Font Availability on Offline Systems**: The Bangla PDF title graphics rely on system or web-font rendering of *Hind Siliguri*. If an offline system blocks web-fonts and has no Bengali system font installed, titles gracefully fall back to document order indicators `[Doc #X]`.
* **Browser Memory with Very Large Files**: Merging numerous massive PDF documents simultaneously is bound by the client browser's available memory. An automatic warning toast is displayed when total uploaded size exceeds 50MB.

---

## License

MIT License

Copyright (c) 2026 Nuhan Aslam

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
