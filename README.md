# AP Pulse — AI-Powered Invoice Compliance Hub for NBFCs

> An end-to-end accounts payable automation tool that uses OCR and LLMs to extract invoice data, validate GST/TDS compliance, and log decisions for audit — built for NBFC finance operations teams.

---

## Problem

NBFC accounts payable teams manually verify GST applicability, TDS sections (194C, 194J, 194Q, etc.), and invoice fields across hundreds of vendor invoices each month. This process is:
- Time-consuming and error-prone
- Difficult to audit with no structured decision log
- Disconnected from payment gateway reconciliation (SmartHub, PayZapp, MPR)

## Solution

AP Pulse is a Streamlit-based compliance assistant that automates the end-to-end invoice review workflow:

1. **OCR Extraction** — Uploads invoice PDFs/images and extracts structured data using `pdfplumber` and `cv2`
2. **LLM Analysis** — Sends extracted data to Groq (LLaMA) for intelligent field classification and anomaly detection
3. **Compliance Engine** — Validates GST applicability, TDS sections, PAN, GSTIN, and vendor limits against rule-based checks
4. **Audit Logging** — Records every decision (pass/fail/flag) to Google Sheets and SQLite for traceability
5. **Dashboard** — Displays compliance status, vendor risk scores, and flagged invoices in a clean Streamlit UI

---

## Tech Stack

| Layer | Technology |
|---|---|
| UI | Streamlit |
| OCR | pdfplumber, OpenCV (cv2), base64 |
| LLM | Groq API (LLaMA 3) |
| Compliance Rules | Python rules engine (custom) |
| Data Store | SQLite, Google Sheets (gspread) |
| Auth | Google OAuth2 Service Account / st.secrets |
| Deployment | Streamlit Cloud |

---

## Key Features

- GST and TDS section auto-detection (194C, 194J, 194Q, 194A, 194H)
- Multi-state tax rule handling (configurable by state)
- Vendor limit tracking and threshold alerts
- Structured audit trail with timestamps and decision rationale
- Modular engine — ComplianceEngine, SheetsConnector, UIEngine are independently maintainable

---

## Project Structure

```
AP-Pulse/
├── Invoice_Hub.py        # Main Streamlit app entry point
├── ComplianceEngine.py   # Core GST/TDS rules engine + LLM integration
├── SheetsConnector.py    # Google Sheets read/write + SQLite persistence
├── UIEngine.py           # Reusable UI components
├── Compliance.py         # PDF extraction and compliance audit runner
├── Compliance_app.py     # Standalone compliance checker
├── Budget Tracker.py     # Budget monitoring module
├── .gitignore
└── README.md
```

---

## Setup & Running Locally

### 1. Clone the repo
```bash
git clone https://github.com/MAHAYAVANSHI2904/AP-Pulse.git
cd AP-Pulse
```

### 2. Create a virtual environment
```bash
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
```

### 3. Install dependencies
```bash
pip install streamlit gspread google-auth pdfplumber opencv-python pandas groq
```

### 4. Configure secrets
Create `.streamlit/secrets.toml`:
```toml
GROQ_API_KEY = "your_groq_api_key"
SHEET_ID = "your_google_sheet_id"

[gcp_service_account]
type = "service_account"
project_id = "your_project_id"
# ... rest of your service account JSON fields
```

### 5. Run the app
```bash
streamlit run Invoice_Hub.py
```

---

## Security

- No credentials, API keys, or database files are committed to this repo
- All secrets are managed via `st.secrets` (Streamlit Cloud) or environment variables
- See `SECURITY.md` for responsible disclosure

---

## Product Context

Built as a solo product + engineering project to demonstrate:
- **Domain expertise**: NBFC AP operations, Indian tax law (GST, TDS), invoice reconciliation
- **Product thinking**: Identified a real workflow pain point, scoped MVP, iterated on compliance rules
- **Technical execution**: OCR pipeline, LLM prompt engineering, rules engine, audit logging, Streamlit deployment

Targeted at Associate Product Manager roles in fintech and finance automation.

---

## Author

**Chirag Mahayavanshi** — Finance professional transitioning to Product Management  
5+ years in NBFC operations, accounts payable, and financial reporting  
Mumbai, India  
[LinkedIn](https://linkedin.com/in/chiragmahayavanshi) | [GitHub](https://github.com/MAHAYAVANSHI2904)
