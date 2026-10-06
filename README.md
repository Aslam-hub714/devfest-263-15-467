# Tender Document Package Builder

> An enterprise-grade, single-file client-side web application for public and institutional procurement officers, tender evaluators, and bidding enterprises. Assembles verified submission dossiers conforming to public procurement acts (PPA/PPR) and e-GP formatting with cryptographic duplicate verification, automated requirement matching, bilingual rendering, and audit-ready checklist generation.

---

## 📋 Overview

The **Tender Document Package Builder** simplifies the compilation of complex tender bids. It loads standardized tender requirements, performs real-time compliance matching against uploaded PDF documents, computes cryptographic hashes to reject fraud and duplication, validates expiry deadlines, stamps authorized digital seals, and generates an official merged PDF dossier with an automated cover page, sequential Table of Contents (Index), and uniform page-numbered footers.

---

## 🚀 Key Features Summary

### 1. Cryptographic Document Ingestion & SHA-256 Duplicate Protection
* **Instant Duplicate Detection**: Asynchronously calculates SHA-256 binary checksums for every uploaded PDF file using **Crypto-JS** with visual loading spinners.
* **Fraud Prevention Policy**: Files sharing an identical SHA-256 hash are tagged as duplicates and strictly prevented from being matched to multiple requirements.
* **Robust File Validation**: Wraps all **pdf-lib** and **pdf.js** loading in fault-tolerant `try-catch` blocks. Corrupt, password-protected, or unreadable PDFs are flagged with a prominent `"Corrupt or Protected PDF"` badge and bypassed gracefully during dossier assembly without crashing the runtime.
* **Large File Warning**: Triggers a notification if total uploaded documents exceed 50MB.

### 2. Dynamic Requirement Status System & Instant Reactivity
* Automatically computes strict compliance states with instant UI reactivity across matching, unmatching, date picking, or document deletion:
  * **OK (Green)**: Matched with valid, non-expired document.
  * **Missing (Red)**: Mandatory tender requirement without an attached file (blocks generation).
  * **Expiry date needed (Yellow)**: Requirement with `has_expiry = true` matched to a file without an entered expiry date (blocks generation).
  * **Expired (Red)**: Document expires strictly before the tender submission deadline (blocks generation; expiry *on* deadline day is compliant).
  * **Not provided (Gray)**: Optional document not provided (allowed; skipped from the merged package).
* **Generation Lock**: The **"Generate Package"** and **"Submission Export"** buttons are dynamically disabled whenever any blocking issue or duplicate file error is detected.

### 3. Automated Fuzzy Matching
* Intelligent string similarity algorithm that compares uploaded PDF filenames against bilingual requirement titles (`title_en` and `title_bn`) to automatically match files with single-click operation.

### 4. Official Submission Package Assembly (`pdf-lib`)
* **Page 1 (Official Cover)**: Stamped with Tender ID, Tender Title, Procuring Entity, Bidder Name, Submission Deadline, Generation Timestamp, and legal declaration.
* **Page 2 (Table of Contents / Index)**: Lists each included document, its matched file name, page count, and **exact starting page number** in the final merged document.
* **Pages 3+ (Document Assembly)**: Appends verified donor documents in exact requirement order, skipping unmatched optional files.
* **Uniform Footers**: Stamped across all pages in the format: `<tender_id> | Page X of Y` (accounting for Cover Page 1, Index Page 2, and all attached pages).
* **Digital Seal / Signature Overlay**: Custom transparent PNG upload or sample vector seal generator with customizable stamp scope (All Pages, Cover Only, or Documents Only).

### 5. In-Browser PDF Preview (`pdf.js`)
* Interactive modal preview rendering first-page thumbnails and high-resolution document previews directly in the browser with zoom and pagination controls.

### 6. Audit & Export Deliverables
* **Submission Export**: Instant download of the finalized merged PDF named `<tender_id>_Package.pdf` (e.g. `TND-2026-BD-8902_Package.pdf`).
* **Checklist Export (`.xlsx`)**: Generates an audit sheet using **SheetJS** named `<tender_id>_Checklist.xlsx` containing:
  * Requirement ID
  * Document Title (EN & BN)
  * Matched File Name
  * Page Count
  * Expiry Date
  * Status
* **Submission Folder Pack**: One-click download of all three artifacts: Package PDF, Excel Checklist, and Project State JSON.

### 7. Screenshot-Ready Submission View
* High-contrast toggle mode designed for documentation and committee review captures (`screenshots/`). Collapses configuration cards and displays an expanded, high-contrast matrix with prominent compliance badges.

### 8. Bilingual Support (English & Bangla বাংলা)
* Instant toggle switching between English and Bengali (`title_en` and `title_bn`).
* Graceful fallback rendering for PDF standard fonts and canvas graphics ensures Bengali titles render crisply without Unicode font corruption.

---

## 🛠️ Tech Stack & Libraries (CDN-Only Architecture)

| Technology | Version / CDN | Purpose |
|---|---|---|
| **Vue 3** | `3.3.4` (unpkg) | Reactive state management, computed requirement engine, and UI components |
| **Tailwind CSS** | CDN (`cdn.tailwindcss.com`) | Responsive, high-contrast design system and theme badges |
| **pdf-lib** | `1.17.1` (unpkg) | Client-side PDF generation, page copying, text layout, and footer stamping |
| **Crypto-JS** | `4.1.1` (cdnjs) | SHA-256 cryptographic hashing for duplicate detection |
| **SheetJS (XLSX)** | `0.20.1` (cdn.sheetjs.com) | Audit-ready Excel `.xlsx` checklist workbook generation |
| **pdf.js** | `3.11.174` (cdnjs) | In-browser thumbnail and multi-page preview rendering |

---

## ⚡ Quickstart & Local Setup

Because the application is built as a zero-build, CDN-backed single-file HTML application, it can be served using any static web server or opened directly in a modern web browser.

### Option 1: Serve with Vite / Node (Included Workspace)
```bash
# 1. Install dependencies
npm install

# 2. Launch development server
npm run dev

# App runs on http://localhost:3000
```

### Option 2: Run with Any Static HTTP Server
```bash
# Python 3
python -m http.server 8080

# Or npx serve
npx serve .
```

### Option 3: Direct Browser File Launch
Simply open `index.html` directly in Google Chrome, Microsoft Edge, Mozilla Firefox, or Safari.

---

## 📝 Summary of AI Prompts & Engineering History

For competition compliance and Git commit provenance, the following prompt iterations guided the architecture:

1. **Initial Architecture Prompt**:
   - Specified tech stack: Vue 3 CDN, Tailwind CSS, pdf-lib 1.17.1, Crypto-JS 4.1.1, SheetJS 0.20.1.
   - Built core requirement matrix: `requirements.json` ingestion, multi-PDF uploader, dynamic status calculation ('Missing', 'Expiry date needed', 'Expired', 'Not provided', 'OK'), SHA-256 duplicate detection, Cover page generation, and uniform footer stamping.
2. **Feature Enhancement & Bonus Capabilities**:
   - Integrated fuzzy Auto-Match algorithm comparing filenames with bilingual titles.
   - Built PNG official seal/stamp overlay with configurable scope.
   - Added project state persistence (`localStorage` and `<tender_id>_session.json` backup).
   - Hardened PDF parser with encrypted/corrupt detection badges.
3. **Table of Contents, Bengali Rendering & PDF.js Preview**:
   - Implemented Table of Contents (Page 2) with exact starting page calculations offsetting Cover (P.1) and TOC (P.2), starting donor documents at Page 3.
   - Engineered canvas-to-PNG renderer for Bengali font fidelity.
   - Integrated `pdf.js` interactive viewer modal with eye-icon buttons.
   - Added "Export Submission Folder Pack" (PDF + XLSX + JSON).
4. **Final Edge-Case Hardening & Submission Utilities**:
   - Implemented async Crypto-JS SHA-256 hashing with animated spinners on file cards.
   - Wrapped all PDF loading in safe `try-catch` blocks with `"Corrupt or Protected PDF"` badges and non-blocking merge bypass.
   - Added 50MB file size warning toast.
   - Created dedicated "Submission Export" button for `<tender_id>_Package.pdf` and exact column alignment for `<tender_id>_Checklist.xlsx`.
   - Built "Submission View" toggle for high-contrast evaluation screenshot capture.

---

## 📄 License
Apache-2.0 License. Built for procurement excellence.
