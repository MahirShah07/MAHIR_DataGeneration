# MAHIR NSE 5-minute OHLCV dataset

Frozen 5-minute price and volume bars for 20 NSE-listed stocks, prepared for the MAHIR study
*"MAHIR: A Bounded-Impact Market-State Framework for Indian Equity Execution Simulation and Its Limits as a Short-Horizon Forecaster"*.
`MAHIR_Simulation.ipynb` reads these files, so every run of the simulation uses exactly the same data.

## At a glance

| | |
|---|---|
| Source | TradingView via tvDatafeed (unofficial) |
| Exchange | NSE (cash equities) |
| Bar size | 5 minutes |
| Window requested | 2026-07-16 to 2026-10-10 (end exclusive) |
| Coverage obtained | 2026-07-16 to 2026-10-09, 60 sessions per stock |
| Downloaded (UTC) | 2026-10-17 07:22:28 |
| Time zone | Asia/Kolkata (IST, +05:30) |
| Stocks | 20: 15 large-cap and 5 lower-liquidity |
| Total bars | 87,120 |
| Failed downloads | None. |

## Files

* `<SYMBOL>_NS.csv`: one file per stock, for example `RELIANCE_NS.csv`. The dot in the `.NS` symbol becomes an underscore.
* `manifest.json`: machine-readable provenance. For every file it lists rows, sessions, first and last timestamp, bars per session, missing-bar share, zero-volume bars, price range, volume statistics and the full SHA-256 hash.
* `README.md`: this file.

## Schema

| Column | Type | Meaning |
|---|---|---|
| `Datetime` | ISO-8601 timestamp with offset, e.g. `2026-07-20 09:15:00+05:30` | Start time of the 5-minute bar, IST |
| `Open` | float, INR | First price in the bar |
| `High` | float, INR | Highest price in the bar |
| `Low` | float, INR | Lowest price in the bar |
| `Close` | float, INR | Last price in the bar |
| `Volume` | integer | Shares traded in the bar, as reported by the source |

Rows are sorted by `Datetime`. A full NSE session runs from 09:15 to 15:30 IST, which gives 75 bars; the first bar starts at 09:15 and the last at 15:25.

## Collection method

| Setting | Value |
|---|---|
| Client | `tvDatafeed` (installed from https://github.com/rongardF/tvdatafeed.git) |
| Request | exchange `NSE`, interval 5 minutes, up to 5,000 bars per stock |
| Login | anonymous |
| Retries | 3 per stock, with increasing pauses |
| Library versions | pandas 2.2.3, tvdatafeed 2.1.0, Python 3.13.16 |

## Processing

Only these steps were applied after download. Nothing else was changed.

1. Timestamps converted to Asia/Kolkata. The source returns timestamps without a time zone, so the script checks whether they fit NSE trading hours as IST or as UTC and converts accordingly.
2. Only the continuous session was kept: bars starting from 09:15 up to, but not including, 15:30 IST.
3. Rows with missing values or non-positive prices were dropped, and duplicate timestamps were removed.
4. Bars outside the requested window were dropped, and a bar still forming at download time was dropped.
5. No outlier filtering, no gap filling and no additional price adjustment. Whether the vendor already adjusted prices for corporate actions was not verified.

## Coverage and completeness

| Symbol | Tier | Bars | Sessions | First bar (IST) | Last bar (IST) | Min bars/session | Max bars/session | Missing bars (%) | Zero-volume bars |
|---|---|---|---|---|---|---|---|---|---|
| RELIANCE.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| TCS.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| HDFCBANK.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| INFY.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| ICICIBANK.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| SBIN.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| ITC.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| LT.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| BHARTIARTL.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| HINDUNILVR.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| AXISBANK.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| MARUTI.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| SUNPHARMA.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| TITAN.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| NTPC.NS | large-cap | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| FEDERALBNK.NS | lower-liquidity | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| IDFCFIRSTB.NS | lower-liquidity | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| PNB.NS | lower-liquidity | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| NHPC.NS | lower-liquidity | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |
| YESBANK.NS | lower-liquidity | 4356 | 60 | 2026-07-16 09:15 | 2026-10-09 15:10 | 72 | 75 | 3.2 | 0 |

"Missing bars" is the average shortfall against 75 bars per session. Large-cap stocks are the first 15 in the list; the last 5 are the lower-liquidity group.

## Price and volume summary

| Symbol | Lowest close (INR) | Highest close (INR) | Median bar volume | Total volume |
|---|---|---|---|---|
| RELIANCE.NS | 1162.1 | 1341.0 | 103,237 | 642,974,603 |
| TCS.NS | 2036.9 | 2491.7 | 27,330 | 192,437,374 |
| HDFCBANK.NS | 683.85 | 821.0 | 265,206 | 1,681,122,230 |
| INFY.NS | 981.0 | 1193.3 | 89,225 | 601,339,866 |
| ICICIBANK.NS | 1291.5 | 1471.0 | 86,108 | 585,993,574 |
| SBIN.NS | 938.15 | 1120.3 | 79,312 | 530,351,729 |
| ITC.NS | 253.4 | 292.05 | 110,823 | 937,938,062 |
| LT.NS | 3610.3 | 4119.0 | 14,373 | 90,594,986 |
| BHARTIARTL.NS | 1728.9 | 2005.1 | 47,207 | 335,284,364 |
| HINDUNILVR.NS | 1828.5 | 2209.0 | 10,759 | 82,628,305 |
| AXISBANK.NS | 1177.8 | 1332.1 | 52,558 | 353,348,860 |
| MARUTI.NS | 11232.0 | 14300.0 | 3,309 | 22,730,448 |
| SUNPHARMA.NS | 1748.1 | 2046.0 | 14,001 | 105,132,315 |
| TITAN.NS | 4350.0 | 5181.7 | 5,652 | 44,679,268 |
| NTPC.NS | 308.8 | 351.4 | 60,781 | 486,474,368 |
| FEDERALBNK.NS | 314.45 | 363.7 | 45,921 | 358,769,422 |
| IDFCFIRSTB.NS | 78.1 | 89.45 | 150,411 | 1,198,681,933 |
| PNB.NS | 104.65 | 119.27 | 109,773 | 887,258,514 |
| NHPC.NS | 70.3 | 82.92 | 64,671 | 611,015,450 |
| YESBANK.NS | 20.38 | 23.95 | 510,212 | 3,754,183,180 |

## File inventory

| File | Rows | SHA-256 (first 16 characters; full hash in manifest.json) |
|---|---|---|
| RELIANCE_NS.csv | 4356 | 61d090bde22f878f... |
| TCS_NS.csv | 4356 | ea63cfed58c7a1a0... |
| HDFCBANK_NS.csv | 4356 | dfe55e0e7bdddb42... |
| INFY_NS.csv | 4356 | f747c0f18036d4b7... |
| ICICIBANK_NS.csv | 4356 | 72845519b23979e1... |
| SBIN_NS.csv | 4356 | b1f90772230b947d... |
| ITC_NS.csv | 4356 | b3bfacacb6618280... |
| LT_NS.csv | 4356 | 8d2fa86bd30c6ddd... |
| BHARTIARTL_NS.csv | 4356 | 9e64907913f74707... |
| HINDUNILVR_NS.csv | 4356 | 4f3f9fa343944494... |
| AXISBANK_NS.csv | 4356 | 61365dc97390bbc5... |
| MARUTI_NS.csv | 4356 | 1d470dde222dd357... |
| SUNPHARMA_NS.csv | 4356 | bd857058c8b4ba4d... |
| TITAN_NS.csv | 4356 | 3ea2356005ca5f8e... |
| NTPC_NS.csv | 4356 | 9bff1c67b455b34a... |
| FEDERALBNK_NS.csv | 4356 | 7372b81428a2072b... |
| IDFCFIRSTB_NS.csv | 4356 | 81469f32df8b27f6... |
| PNB_NS.csv | 4356 | 1aa4abdfa93aa6f4... |
| NHPC_NS.csv | 4356 | 20fe9b37ff1b91fa... |
| YESBANK_NS.csv | 4356 | 24cefe2ba2550330... |

## Known limitations

* The data come from a retail charting service through an unofficial client, not from an exchange feed. Bars can be missing, volumes can differ from exchange records, and the vendor may revise history. Do not treat the files as an official record.
* Lower-liquidity stocks have more missing and zero-volume bars.
* The source returns a limited number of bars (about 66 sessions of 5-minute data), so the window cannot be extended backwards later.
* The files are a snapshot. Downloading again on another day gives a different window and may give slightly different bars.
* Results computed from this dataset are not directly comparable with results from a different vendor or download date.

## Load the data

```python
import pandas as pd
df = pd.read_csv("RELIANCE_NS.csv", parse_dates=["Datetime"])
print(df.head())
print(df["Datetime"].dt.date.nunique(), "sessions")
```

## Check file integrity

```python
import hashlib, json
m = json.load(open("manifest.json"))
for f in m["files"]:
    h = hashlib.sha256(open(f["file"], "rb").read()).hexdigest()
    print(f["file"], "OK" if h == f["sha256"] else "MODIFIED")
```

## Terms of use

The bars originate from a third-party vendor and from NSE. Vendor and exchange terms may limit redistribution and commercial use, and the vendor library used here is unofficial. Check those terms before making this repository public or reusing the files. The script that produced this dataset is `MAHIR_Data_Generation.ipynb`.

