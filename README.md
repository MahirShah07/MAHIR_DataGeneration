<div align="center">

# 📊 MAHIR — NSE 5-Minute OHLCV Data Generation

**Official Data Pipeline & Frozen Intraday Dataset for the MAHIR Research Framework**

[![View on Google Colab](https://img.shields.io/badge/Open%20in%20Colab-F9AB00?style=for-the-badge&logo=googlecolab&logoColor=white)](https://colab.research.google.com/drive/1rAaU0DY5d2ESvfDZLqOzNlidMyIgIDm8)


[![Python](https://img.shields.io/badge/Python-3.13%2B-3776AB?style=curved-square&logo=python&logoColor=white)](https://www.python.org/)
[![Pandas](https://img.shields.io/badge/Pandas-2.2.3-150458?style=curved-square&logo=pandas&logoColor=white)](https://pandas.pydata.org/) 

</div>

---

## Table of Contents

- [Overview](#overview)
- [Dataset at a Glance](#dataset-at-a-glance)
- [Architecture & Pipeline](#architecture--pipeline)
- [Equity Universe](#equity-universe)
- [Repository Structure](#repository-structure)
- [Dataset Schema](#dataset-schema)
- [Download](#download)
- [Quick Start](#quick-start)
- [Cryptographic Integrity Verification](#cryptographic-integrity-verification)
- [Data Provenance & Terms of Use](#data-provenance--terms-of-use)

---

## Download

The complete frozen dataset (`OHLCVData/` — 20 CSVs + `manifest.json`) is available as a single compressed archive:

| Resource | Link | Size |
|:---|:---|:---|
| **OHLCVData.zip** (all 20 stocks) | [![Google Drive](https://img.shields.io/badge/Download-Google%20Drive-4285F4?style=curved-square&logo=googledrive&logoColor=white)](https://drive.google.com/file/d/1iXge_V2yHgj7xYc-HTf8idOw2o4lkPZV/view?usp=sharing) [![GitHub](https://img.shields.io/badge/Download-GitHub%20-8534F3?style=curved-square&logo=github&logoColor=white)](https://github.com/MahirShah07/MAHIR_DataGeneration/blob/main/OHLCVData.zip) | 1.14 MB |
| **OHLCVData Folder** (all 20 stocks) | [![Google Drive](https://img.shields.io/badge/Download-Google%20Drive-4285F4?style=curved-square&logo=googledrive&logoColor=white)](https://drive.google.com/drive/folders/1gLLvr-o_kJCCIZ8rXTRoih6BtvaydNjX?usp=sharing) [![GitHub](https://img.shields.io/badge/Download-GitHub%20-8534F3?style=curved-square&logo=github&logoColor=white)](https://github.com/MahirShah07/MAHIR_DataGeneration/tree/main/OHLCVData) | 5.4 MB |
| **Colab Notebook** | [![View on Google Colab](https://img.shields.io/badge/Open%20in%20Colab-F9AB00?style=curved-square&logo=googlecolab&logoColor=white)](https://colab.research.google.com/drive/1rAaU0DY5d2ESvfDZLqOzNlidMyIgIDm8) [![Google Drive](https://img.shields.io/badge/Download-Google%20Drive-4285F4?style=curved-square&logo=googledrive&logoColor=white)](https://drive.google.com/file/d/1rAaU0DY5d2ESvfDZLqOzNlidMyIgIDm8/view?usp=sharing) [![GitHub](https://img.shields.io/badge/Download-GitHub%20-8534F3?style=curved-square&logo=github&logoColor=white)](https://github.com/MahirShah07/MAHIR_DataGeneration/blob/main/MAHIR%20Data%20Generation.ipynb) | 40 KB |
| **Project**  | [![Google Drive](https://img.shields.io/badge/Download-Google%20Drive-4285F4?style=curved-square&logo=googledrive&logoColor=white)](https://drive.google.com/drive/folders/1TdW84ZjOfqrt7wEdThUUUpZ1CS5qQIfD?usp=sharing) [![GitHub](https://img.shields.io/badge/Download-GitHub%20-8534F3?style=curved-square&logo=github&logoColor=white)](https://github.com/MahirShah07/MAHIR_DataGeneration) | 9.6 MB |

After downloading, unzip it and place the resulting `OHLCVData/` folder at the root of the repository. All SHA-256 checksums in `manifest.json` will verify correctly against these files.

---

## Overview

This repository is the **companion data generation pipeline** for the MAHIR research paper. Its sole purpose: produce a **frozen, cryptographically-sealed intraday dataset** that enables perfectly deterministic reproducibility of every simulation run described in the paper.


### Key Design Principles

| Principle | Implementation |
|:---|:---|
| **Determinism** | Frozen snapshot; no live feeds during simulation |
| **Traceability** | `manifest.json` logs exact library versions, download timestamps, and per-file SHA-256 checksums |
| **Transparency** | Full cleaning rules documented; no hidden transformations |

---

## Dataset at a Glance

| Property | Value |
|:---|:---|
| **Exchange** | National Stock Exchange of India (NSE) |
| **Equities** | 20 (15 large-cap + 5 lower-liquidity) |
| **Bar Resolution** | 5 minutes |
| **Session Window (IST)** | 09:15 to 15:25 (75 bars/day) |
| **Trading Sessions** | 60 complete sessions per stock |
| **Date Range** | 2026-07-16 to 2026-10-09 (IST) |
| **Total Bars** | 87,120 (4,356 bars x 20 stocks) |
| **Missing Bars** | 3.2% average (IST holiday/gap artefacts) |
| **Zero-Volume Bars** | 0 across all stocks |
| **Data Source** | TradingView via tvDatafeed (unofficial client) |
| **Fetched At (UTC)** | 2026-10-17 07:22:28 |
| **Pandas** | 2.2.3 |
| **tvdatafeed** | 2.1.0 |
| **Failed Downloads** | None — all 20 stocks complete |

---

## Architecture & Pipeline

The `MAHIR Data Generation.ipynb` notebook automates a strict **4-stage pipeline**:

```
+---------------------------------------------------------------------+
|  Stage 1 - RAW ACQUISITION (tvDatafeed)                             |
|  - Queries 5-min intraday bars from NSE cash equities               |
|  - Up to 5,000 bars per symbol                                      |
|  - Automated retry with exponential back-off (3 attempts)           |
+---------------------------+-----------------------------------------+
                            |
                            v
+---------------------------------------------------------------------+
|  Stage 2 - TIME-ZONE NORMALIZATION & FILTERING (clean_bars)         |
|  - Detects naive/UTC timestamps; normalizes to IST (Asia/Kolkata)   |
|  - Keeps only continuous session bars: 09:15 <= bar start < 15:30   |
|  - Removes non-positive prices, duplicate records, NaNs             |
|  - Drops any still-forming bar at download time                     |
+---------------------------+-----------------------------------------+
                            |
                            v
+---------------------------------------------------------------------+
|  Stage 3 - CRYPTOGRAPHIC PACKAGING (manifest.json)                  |
|  - Writes one <SYMBOL>_NS.csv per equity                            |
|  - Generates manifest.json with library versions, session counts,   |
|    missing-bar %, volume stats, and per-file SHA-256 checksums      |
|  - Auto-generates OHLCVData/README.md (granular dataset card)       |
+---------------------------+-----------------------------------------+
                            |
                            v
+---------------------------------------------------------------------+
|  Stage 4 - REMOTE SIMULATION INGESTION                              |
|  - Packages OHLCVData/ into OHLCVData.zip                           |
|  - MAHIR_Simulation.ipynb streams CSVs via raw GitHub URLs          |
|  - Every simulation run is guaranteed to use the same frozen data   |
+---------------------------------------------------------------------+
```

---

## Equity Universe

The dataset stratifies **20 NSE equities** into two liquidity regimes — **87,120 total 5-minute bars** — to evaluate bounded market impact across varying order-book depths:

### Tier 1: Large-Cap (15 stocks)

| Symbol | Bars | Sessions | Close Range (INR) | Median Bar Volume |
|:---|---:|---:|:---|---:|
| RELIANCE.NS | 4,356 | 60 | 1,162.10 – 1,341.00 | 103,237 |
| TCS.NS | 4,356 | 60 | 2,036.90 – 2,491.70 | 27,330 |
| HDFCBANK.NS | 4,356 | 60 | 683.85 – 821.00 | 265,206 |
| INFY.NS | 4,356 | 60 | 981.00 – 1,193.30 | 89,225 |
| ICICIBANK.NS | 4,356 | 60 | 1,291.50 – 1,471.00 | 86,108 |
| SBIN.NS | 4,356 | 60 | 938.15 – 1,120.30 | 79,312 |
| ITC.NS | 4,356 | 60 | 253.40 – 292.05 | 110,823 |
| LT.NS | 4,356 | 60 | 3,610.30 – 4,119.00 | 14,373 |
| BHARTIARTL.NS | 4,356 | 60 | 1,728.90 – 2,005.10 | 47,207 |
| HINDUNILVR.NS | 4,356 | 60 | 1,828.50 – 2,209.00 | 10,759 |
| AXISBANK.NS | 4,356 | 60 | 1,177.80 – 1,332.10 | 52,558 |
| MARUTI.NS | 4,356 | 60 | 11,232.00 – 14,300.00 | 3,309 |
| SUNPHARMA.NS | 4,356 | 60 | 1,748.10 – 2,046.00 | 14,001 |
| TITAN.NS | 4,356 | 60 | 4,350.00 – 5,181.70 | 5,652 |
| NTPC.NS | 4,356 | 60 | 308.80 – 351.40 | 60,781 |

### Tier 2: Lower-Liquidity (5 stocks)

| Symbol | Bars | Sessions | Close Range (INR) | Median Bar Volume |
|:---|---:|---:|:---|---:|
| FEDERALBNK.NS | 4,356 | 60 | 314.45 – 363.70 | 45,921 |
| IDFCFIRSTB.NS | 4,356 | 60 | 78.10 – 89.45 | 150,411 |
| PNB.NS | 4,356 | 60 | 104.65 – 119.27 | 109,773 |
| NHPC.NS | 4,356 | 60 | 70.30 – 82.92 | 64,671 |
| YESBANK.NS | 4,356 | 60 | 20.38 – 23.95 | 510,212 |

> **Date range for all stocks:** 2026-07-16 09:15 to 2026-10-09 15:10 (IST)
> **Missing bars:** 3.2% per stock (average shortfall vs. 75 bars/session, attributable to NSE holidays and vendor gaps)

---

## Repository Structure

```
MAHIR_DataGeneration/
|
+-- MAHIR Data Generation.ipynb   # End-to-end acquisition, cleaning & packaging notebook
|                                  # Run this to regenerate OHLCVData/ from scratch
|
+-- OHLCVData/                    # Frozen dataset (20 stocks, SHA-256 verified)
|   +-- RELIANCE_NS.csv           #   5-minute OHLCV bars in IST (+05:30)
|   +-- TCS_NS.csv
|   +-- HDFCBANK_NS.csv
|   +-- INFY_NS.csv
|   +-- ICICIBANK_NS.csv
|   +-- SBIN_NS.csv
|   +-- ITC_NS.csv
|   +-- LT_NS.csv
|   +-- BHARTIARTL_NS.csv
|   +-- HINDUNILVR_NS.csv
|   +-- AXISBANK_NS.csv
|   +-- MARUTI_NS.csv
|   +-- SUNPHARMA_NS.csv
|   +-- TITAN_NS.csv
|   +-- NTPC_NS.csv
|   +-- FEDERALBNK_NS.csv
|   +-- IDFCFIRSTB_NS.csv
|   +-- PNB_NS.csv
|   +-- NHPC_NS.csv
|   +-- YESBANK_NS.csv
|   +-- manifest.json             #   Provenance metadata & SHA-256 checksums for all files
|   +-- README.md                 #   Auto-generated granular dataset card
|
+-- OHLCVData.zip                 # Compressed archive of OHLCVData/ (1.2 MB)
|
+-- README.md                     # This file
```

---

## Dataset Schema

Every `<SYMBOL>_NS.csv` file inside `OHLCVData/` adheres to the following strict schema:

| Column | Type | Description |
|:---|:---|:---|
| `Datetime` | ISO-8601 string with offset | Bar **start** timestamp in IST (e.g. `2026-07-16 09:15:00+05:30`). Spans 09:15 to 15:25. |
| `Open` | float64 | Opening trade price in the 5-minute interval (INR) |
| `High` | float64 | Highest trade price in the 5-minute interval (INR) |
| `Low` | float64 | Lowest trade price in the 5-minute interval (INR) |
| `Close` | float64 | Closing trade price in the 5-minute interval (INR) |
| `Volume` | int64 | Total shares traded during the 5-minute interval |

Rows are sorted by `Datetime` ascending. A full NSE continuous session runs 09:15 to 15:30 IST (75 bars); the last bar starts at 15:25.

**Sample rows** from `RELIANCE_NS.csv`:

```
Datetime,Open,High,Low,Close,Volume
2026-07-16 09:15:00+05:30,1263.5,1272.0,1258.3,1265.8,312450
2026-07-16 09:20:00+05:30,1265.8,1270.0,1262.1,1266.3,198732
2026-07-16 09:25:00+05:30,1266.3,1268.5,1260.5,1261.4,154981
```

---

## Quick Start

### 1. Load a Single Stock Locally

```python
import pandas as pd

df = pd.read_csv(
    "OHLCVData/RELIANCE_NS.csv",
    parse_dates=["Datetime"],
    index_col="Datetime"
)

print(f"Loaded {len(df):,} bars across {df.index.normalize().nunique()} trading sessions.")
print(df.head())
```

### 2. Load All 20 Stocks into a Dict

```python
import os
import pandas as pd

DATA_DIR = "OHLCVData"

stocks = {}
for f in sorted(os.listdir(DATA_DIR)):
    if f.endswith(".csv"):
        sym = f.replace("_NS.csv", "") + ".NS"
        stocks[sym] = pd.read_csv(
            os.path.join(DATA_DIR, f),
            parse_dates=["Datetime"],
            index_col="Datetime"
        )

print(f"Loaded {len(stocks)} stocks.")
for sym, df in stocks.items():
    print(f"  {sym:<15} {len(df):>5} bars  {df.index.normalize().nunique()} sessions")
```

### 3. Connect the Simulation Notebook

To run `MAHIR_Simulation.ipynb` against this frozen dataset:

1. Navigate to `OHLCVData/RELIANCE_NS.csv` on GitHub and click **Raw**.
2. Copy the base URL up to the `OHLCVData` directory:
   ```python
   GITHUB_BASE_URL = "https://raw.githubusercontent.com/<your-username>/MAHIR_DataGeneration/main/OHLCVData"
   ```
3. Paste it into `CONFIG["GITHUB_BASE_URL"]` inside `MAHIR_Simulation.ipynb`.

---

## Cryptographic Integrity Verification

Every file in `OHLCVData/` is checksummed in `manifest.json`. Verify your local copies match the exact frozen snapshot used in the paper:

```python
import hashlib, json, os

MANIFEST = "OHLCVData/manifest.json"
DATA_DIR  = "OHLCVData"

with open(MANIFEST) as f:
    manifest = json.load(f)

all_ok = True
for item in manifest["files"]:
    path = os.path.join(DATA_DIR, item["file"])
    h = hashlib.sha256()
    with open(path, "rb") as bf:
        for chunk in iter(lambda: bf.read(1 << 20), b""):
            h.update(chunk)
    digest = h.hexdigest()
    status = "VERIFIED" if digest == item["sha256"] else "MISMATCH"
    if status == "MISMATCH":
        all_ok = False
    print(f"{item['file']:<22}  {item['rows']:>5} rows  {status}")

print()
print("All files verified." if all_ok else "WARNING: One or more files do not match the manifest!")
```

### SHA-256 Quick Reference

| File | Rows | SHA-256 (first 16 chars) |
|:---|---:|:---|
| RELIANCE_NS.csv | 4,356 | `61d090bde22f878f...` |
| TCS_NS.csv | 4,356 | `ea63cfed58c7a1a0...` |
| HDFCBANK_NS.csv | 4,356 | `dfe55e0e7bdddb42...` |
| INFY_NS.csv | 4,356 | `f747c0f18036d4b7...` |
| ICICIBANK_NS.csv | 4,356 | `72845519b23979e1...` |
| SBIN_NS.csv | 4,356 | `b1f90772230b947d...` |
| ITC_NS.csv | 4,356 | `b3bfacacb6618280...` |
| LT_NS.csv | 4,356 | `8d2fa86bd30c6ddd...` |
| BHARTIARTL_NS.csv | 4,356 | `9e64907913f74707...` |
| HINDUNILVR_NS.csv | 4,356 | `4f3f9fa343944494...` |
| AXISBANK_NS.csv | 4,356 | `61365dc97390bbc5...` |
| MARUTI_NS.csv | 4,356 | `1d470dde222dd357...` |
| SUNPHARMA_NS.csv | 4,356 | `bd857058c8b4ba4d...` |
| TITAN_NS.csv | 4,356 | `3ea2356005ca5f8e...` |
| NTPC_NS.csv | 4,356 | `9bff1c67b455b34a...` |
| FEDERALBNK_NS.csv | 4,356 | `7372b81428a2072b...` |
| IDFCFIRSTB_NS.csv | 4,356 | `81469f32df8b27f6...` |
| PNB_NS.csv | 4,356 | `1aa4abdfa93aa6f4...` |
| NHPC_NS.csv | 4,356 | `20fe9b37ff1b91fa...` |
| YESBANK_NS.csv | 4,356 | `24cefe2ba2550330...` |

> Full 64-character hashes are in [`OHLCVData/manifest.json`](OHLCVData/manifest.json).

---

## Regenerating the Dataset

To reproduce or update the dataset from scratch, open `MAHIR Data Generation.ipynb` in Google Colab (recommended) or a local Jupyter environment:

```bash
# Install the required library
pip install git+https://github.com/rongardF/tvdatafeed.git

# Launch the notebook locally
jupyter notebook "MAHIR Data Generation.ipynb"
```

Then **Run All Cells**. The notebook will:
1. Download fresh 5-minute bars for all 20 NSE equities
2. Normalize, clean, and filter to the configured date window
3. Write CSVs + `manifest.json` + `README.md` into `OHLCVData/`
4. Package everything into `OHLCVData.zip`

> **Note:** Re-downloading on a different date will produce a different window. The files in this repository represent the **frozen snapshot** used in the paper (fetched 2026-10-17 07:22:28 UTC). If you regenerate, the SHA-256 checksums will differ from those listed above.

---

## Data Provenance & Terms of Use

- **Source:** Intraday bars were retrieved via [`tvDatafeed`](https://github.com/rongardF/tvdatafeed) — an **unofficial, third-party TradingView client** — targeting the NSE cash equity segment.
- **No price adjustment:** The dataset applies no corporate-action adjustments. Whether the upstream vendor adjusts for splits/dividends was not verified.
- **No gap-filling:** Missing bars are left as-is; no interpolation was applied.
- **Redistribution notice:** Third-party charting vendors and the National Stock Exchange of India (NSE) maintain terms of service regarding bulk data redistribution and commercial usage.
  
---

<div align="center">Made with love for reproducible quantitative research on Indian equity markets.</div>
