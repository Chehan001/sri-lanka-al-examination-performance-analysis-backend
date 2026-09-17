<div align="center">

# Sri Lanka G.C.E. Advanced Level Performance Analysis API

Backend service for processing and analysing Sri Lankan G.C.E. Advanced Level examination data.

</div>

## Overview

This project provides the FastAPI backend for the Sri Lanka G.C.E. Advanced Level Performance Analysis Dashboard. It converts official examination PDF reports and CSV datasets into cleaned, structured data that can be queried by a frontend application.

The API supports data ingestion, validation, PDF table extraction, CSV cleaning, SQLite persistence, analytical summaries, year-to-year comparisons, and exportable datasets.

## Key Features

- Upload and process official A/L examination PDF reports
- Upload CSV files for yearly, province, district, stream, and subject data
- Clean numeric values, percentages, and tabular data with Pandas
- Extract recognised tables from PDF reports with PDFPlumber
- Store processed records in SQLite through SQLAlchemy
- Provide year-wise, geographic, stream-wise, and subject-wise analysis
- Compare performance between two examination years
- Export individual datasets as CSV or all datasets as a ZIP archive
- Expose interactive OpenAPI documentation through FastAPI

## Backend Workflow

The backend follows this workflow from source data to API response:

![Analysis and Dashboard Flow](images/Analysis%20and%20Dashboard%20Flow.png)

## Data Upload and Processing

Uploaded files are saved in their raw form, cleaned and transformed, written to master CSV files, and stored in the SQLite database. Re-uploading a year replaces the existing records for that year in the relevant table.

![Data Upload and Processing Flow](images/Data%20Upload%20and%20Processing%20Flow.png)

## Analytical Modules

The analysis routes expose the main analytical areas used by the dashboard:

![Main Analytical Modules](images/Main%20Analytical%20Modules.png)

## System Architecture

The backend acts as the data-processing and API layer between uploaded examination data, the database, and the React frontend.

![Overall System Flow](images/Overall%20System%20Flow.png)

## Technology Stack

- Python
- FastAPI
- Uvicorn
- Pandas
- PDFPlumber
- SQLAlchemy
- SQLite
- Python Multipart
- OpenPyXL

## Getting Started

### Prerequisites

- Python 3.10 or newer
- A virtual environment
- The frontend running separately if you want to use the complete dashboard

### Installation

Create and activate a virtual environment, then install the dependencies:

```bash
python -m venv venv
```

Windows PowerShell:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

macOS/Linux:

```bash
source venv/bin/activate
pip install -r requirements.txt
```

### Run the API

Start the development server from the backend directory:

```bash
uvicorn main:app --reload
```

The API is available at `http://127.0.0.1:8000`.

- Swagger UI: `http://127.0.0.1:8000/docs`
- ReDoc: `http://127.0.0.1:8000/redoc`
- Health check: `http://127.0.0.1:8000/health`

The application creates the required data directories and SQLite tables during startup.

## API Endpoints

### System

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/` | API welcome message and available links |
| `GET` | `/health` | Service health check |

### Upload

| Method | Endpoint | Description |
| --- | --- | --- |
| `POST` | `/upload-csv` | Upload and process one CSV category for a year |
| `POST` | `/upload-pdf` | Parse and process all recognised tables from an official PDF |

CSV uploads use multipart form fields named `year`, `data_type`, and `file`. Valid data types are `yearly`, `province`, `district`, `stream`, and `subject`. Supported examination years are 2020 through 2025.

### Analysis

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/dashboard-summary` | Return high-level dashboard statistics |
| `GET` | `/year-analysis` | Return yearly eligibility and candidate comparisons |
| `GET` | `/province-analysis` | Return province rankings; optional `year` and `candidate_type` filters |
| `GET` | `/district-analysis` | Return district rankings; optional `year` and `candidate_type` filters |
| `GET` | `/stream-analysis` | Return stream rankings; optional `year` and `candidate_type` filters |
| `GET` | `/subject-analysis` | Return subject pass percentages and grade distributions |
| `GET` | `/compare-years` | Compare two different years using required `year1` and `year2` parameters |

### Export

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/export/{data_type}` | Download one processed dataset as CSV |
| `GET` | `/export/all` | Download all available processed datasets as a ZIP archive |

## Data Storage

```text
data/
├── raw_csv/       # Original CSV uploads
├── raw_pdf/       # Original PDF uploads
└── processed/     # Cleaned master CSV files
```

The SQLite database is stored as `database.db` in the backend root. The database and generated data are ignored by Git so local uploads and runtime data are not committed.

## Project Structure

```text
backend/
├── main.py                    # FastAPI application and CORS configuration
├── populate_data.py           # Utility for loading prepared data
├── requirements.txt           # Python dependencies
├── data/                      # Raw and processed datasets
├── images/                    # Architecture and workflow diagrams
├── models/
│   └── schemas.py             # Request and response schemas
├── routes/
│   ├── analysis_routes.py     # Analytical endpoints
│   ├── export_routes.py       # CSV and ZIP export endpoints
│   └── upload_routes.py       # PDF and CSV upload endpoints
└── services/
	├── analyzer.py            # Analytical calculations
	├── csv_cleaner.py         # Data validation and cleaning
	├── csv_combiner.py        # Raw and master file management
	├── database_service.py    # SQLite and SQLAlchemy operations
	└── pdf_parser.py          # PDF table extraction
```

## CORS Configuration

Local frontend origins on ports `5173` and `3000` are enabled by default. For a deployed frontend, set the `FRONTEND_URL` environment variable before starting the API:

```powershell
$env:FRONTEND_URL = "https://your-frontend-domain.example"
uvicorn main:app --reload
```

## Project Purpose

This backend demonstrates how official education reports can be transformed into a reusable analytical API through document parsing, data preprocessing, database design, REST API development, and export workflows.
