<div align="center">

# ⚡ AP Pulse

### AI-Powered Invoice Compliance Hub for Indian Finance Teams

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)](https://python.org)
[![Streamlit](https://img.shields.io/badge/Streamlit-App-FF4B4B?style=flat&logo=streamlit&logoColor=white)](https://streamlit.io)
[![Groq](https://img.shields.io/badge/LLM-Groq%20LLaMA-F55036?style=flat)](https://groq.com)
[![GST](https://img.shields.io/badge/GST-Compliant-green?style=flat)](https://gst.gov.in)
[![TDS](https://img.shields.io/badge/TDS-Finance%20Act%202024-blue?style=flat)](https://incometax.gov.in)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=flat)](LICENSE)

**Automates invoice OCR → GST/TDS compliance → journal entry generation for AP teams**  
Built for NBFCs and mid-market finance operations in India.

[Live Demo](#) · [Features](#key-features) · [Setup](#setup) · [Architecture](#architecture)

</div>

---

## The Problem

Indian AP teams manually validate every vendor invoice for GST applicability, TDS section mapping (194C, 194J, 194Q...), GSTIN checksums, ITC eligibility, and RCM liability. Across hundreds of invoices per month, this creates:

- High error rate on TDS section misclassification
- No structured audit trail for IT assessments
- Zero vendor intelligence accumulation across seasons

## What AP Pulse Does

AP Pulse is a Streamlit web app that automates the full invoice compliance workflow — from raw PDF upload to a forensic compliance report with journal entries ready for Tally/Zoho Books.

```
PDF / Image Upload
       │
       ▼
 Vision OCR (Groq LLaMA)
       │
       ▼
 30+ GST + TDS Compliance Checks
       │
       ▼
 Audit Trail → SQLite + Google Sheets
       │
       ▼
 Dashboard: Compliance Score, Journal Entry, Tally XML Export
```

---

## Key Features

### Intelligent OCR Engine
- Multi-page PDF → image pipeline using PyMuPDF + OpenCV preprocessing (deskew, denoise, binarize)
- 3-pass extraction: Vision → Math Validation → Vendor Script Generation
- Vendor-specific memory: learns unique invoice layouts on first parse, reuses on subsequent invoices
- Fallback to heuristic regex parser when AI is unavailable

### Compliance Engine (30+ Checks)
| Check | What it validates |
|---|---|
| GSTIN Checksum | Mod-36 algorithm + state code + PAN pattern |
| TDS Section Mapping | 30+ sections mapped to SAC codes and invoice nature (Finance Act 2024) |
| Intra / Inter-State GST | CGST+SGST vs IGST conflict detection |
| ITC Eligibility | Sec 17(5) blocked credit categories |
| RCM Detection | 20+ notified RCM services including import of services |
| 194C Aggregate Tracking | Accumulates vendor payments across invoices, triggers at ₹1L threshold |
| 206AB Risk | Double TDS rate flag for non-filers |
| MSME Payment Terms | 45-day limit tracking per MSMED Act 2006 |
| E-Invoice IRN Mandate | Flags missing IRN on invoices > ₹5L |
| Duplicate Invoice | Session + DB-level deduplication |

### Dashboard Tabs
- **Overview** — KPI row (TDS due, IRN gaps, 206AB risk vendors)
- **Forensic Scans** — Per-invoice compliance scorecard with editable extraction correction
- **TDS Summary** — Section-wise deduction table with payment deadline banner
- **GST Summary** — ITC tracking + GSTR-2B reconciliation upload
- **Vendor Intelligence** — PAN type, state, MSME status, 206AB flag
- **Journal Entries** — Double-entry bookkeeping + Tally XML (`<TALLYMESSAGE>`) export

---

## Tech Stack

| Layer | Technology |
|---|---|
| UI | Streamlit (dark theme, Instrument Serif + Geist) |
| OCR | PyMuPDF, OpenCV, base64 |
| LLM | Groq API — `meta-llama/llama-4-scout-17b-16e-instruct` |
| Compliance Rules | Custom Python rules engine (no external libraries) |
| Database | SQLite (local) / Supabase Postgres (cloud) via SQLAlchemy |
| Audit Logging | Google Sheets via gspread + Google OAuth2 |
| Deployment | Streamlit Cloud |

---

## Architecture

```
ap-pulse/
├── Invoice_Hub.py        # Streamlit entry point — routing, session, page logic
├── ComplianceEngine.py   # GST/TDS rules engine, OCR pipeline, DB layer
├── SheetsConnector.py    # Google Sheets read/write, vendor master sync
├── UIEngine.py           # Reusable Streamlit components, CSS injection
├── invoice_intelligence.py  # 3-pass Gemini/Groq vision parsing engine
├── Compliance.py         # Standalone compliance audit runner
├── requirements.txt
└── .gitignore
```

**Key Design Decisions:**
- `ComplianceEngine.py` is fully stateless — can run outside Streamlit for batch processing
- SQLAlchemy abstraction supports both SQLite (dev) and Postgres (prod) with zero code changes
- TDS section detection uses a priority cascade: Invoice Nature → SAC Code → AI Predicted Section → Manual Review flag

---

## Setup

### Prerequisites
- Python 3.10+
- Groq API key (free tier works): [console.groq.com](https://console.groq.com)
- Google Cloud project with Sheets API enabled (for audit logging)

### 1. Clone

```bash
git clone https://github.com/MAHAYAVANSHI2904/AP-Pulse.git
cd AP-Pulse
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Configure secrets

Create `.streamlit/secrets.toml`:

```toml
GROQ_API_KEY = "gsk_your_key_here"
SHEET_ID = "your_google_sheet_id"

[gcp_service_account]
type = "service_account"
project_id = "your_project_id"
private_key_id = "..."
private_key = "-----BEGIN RSA PRIVATE KEY-----\n...\n-----END RSA PRIVATE KEY-----\n"
client_email = "your-sa@your-project.iam.gserviceaccount.com"
client_id = "..."
token_uri = "https://oauth2.googleapis.com/token"
```

> **Note:** Never commit `secrets.toml`. It's in `.gitignore`.

### 4. Run

```bash
streamlit run Invoice_Hub.py
```

App runs at `http://localhost:8501`

---

## GST & TDS Rule Coverage

AP Pulse covers **Finance Act 2024** and **FY 2025–26 / AY 2026–27**:

- All major TDS sections mapped to new numbering under Sec 393(1)
- 194T (Partner Salary — new in Finance Act 2024)
- Reduced 194H rate (2% from Oct 2024)
- 194IB rate updated to 2% (Budget 2026)
- GSTR-2B reconciliation for ITC risk detection

---

## Security

- No API keys, credentials, or database files committed to this repo
- Secrets managed via `st.secrets` (Streamlit Cloud) or environment variables
- SQLite file (`invoice_forensics.db`) is in `.gitignore`
- Vendor bank details and GSTIN data processed in-memory only

---

## Author

**Chirag Mahayavanshi**  
Senior Finance Executive · ACCA Finalist · Finance + AI Builder  
Mumbai, India

[LinkedIn](https://linkedin.com/in/chiragmahayavanshi) · [GitHub](https://github.com/MAHAYAVANSHI2904) · [Instagram @AiAndAssets](https://instagram.com/aiassets)
