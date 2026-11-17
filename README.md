# SBSZ01: testing a ZARONIA coupon calculation against published cash

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Ikaelelo/sbsz01-zaronia-coupon-check/blob/main/SBSZ01_Colab.ipynb)

Standard Bank's SBSZ01 is a listed floating-rate note paying compounded ZARONIA plus 1.02%. This notebook rebuilds the coupons fixed so far from the SARB's daily ZARONIA fixings and the terms of the pricing supplement, compares them with the issuer's SENS notices (the printed rate and, where read, the rand amount paid), and checks that deliberately wrong methods would have failed. A one-page summary is in [`docs/SBSZ01_summary.pdf`](docs/SBSZ01_summary.pdf).

**To run it:** click the badge, then Runtime → Run all. The notebook fetches its input files from this repository and asks the SARB for the fixings, falling back to a saved copy of the SARB's reply if the API cannot be reached. It uses fixings up to `DATA_AS_OF` (13 November 2026), so every run matches the summary; clear it to include newer coupons. It needs pandas and matplotlib, which Colab already has (`requirements.txt` for a local run).

## Five coupons modelled; two published rates and three published cash amounts compared

| Payment | Calculated | SENS rate | Cash at the calculated rate | Status |
| --- | --- | --- | --- | --- |
| 26 Aug 2025 | 8.1713% | 8.1713% | Ties to the cent on R756m (R15 570 691.99) | rate and cash tie |
| 26 Nov 2025 | 7.9343% | not read | Ties to the cent on R1.796bn (R35 917 815.28) | cash ties |
| 26 Feb 2026 | 7.6815% | not read | | calculated only; fallback applied, unconfirmed |
| 26 May 2026 | 7.6772% | 7.677% | R875.86 above the R33 619 791.45 paid | rate ties, cash differs |
| 26 Aug 2026 | 7.9058% | not read | | calculated only; fallback applied, unconfirmed |

Only the contract's method ties both 2025 amounts. An observation shift matches August 2025 to the cent but misses November by R905.38, so one matching period proves little. May 2026 ties only with the benchmark rounded to three decimals, which misses both 2025 amounts: the published figures are consistent with four-decimal rounding in 2025 and three in May 2026, a 0.02 bp difference that the notice alone cannot confirm.

## What is where

| Path | What it holds |
| --- | --- |
| `SBSZ01_Colab.ipynb` | The method: calendar, coupon engine, checks, convention test, pricing. Saved with its outputs. |
| `inputs/sbsz01_terms.json` | The note's terms, each with its clause in the pricing supplement. |
| `inputs/sens_notices.csv` | The issuer's SENS notices: rate as printed, interest paid, source. |
| `inputs/nominal_history.csv` | The amount in issue over time, from the supplement and the tap notice. |
| `inputs/repo_rate_changes.csv` | SARB repo rate decisions (history chart only). |
| `inputs/declared_holidays.csv` | One-off public holidays; regular ones, Easter included, are calculated by rule. |
| `data/sarb_MMRD855A_cache.json` | The SARB API's reply saved byte for byte, with its SHA-256 in the `.meta.json` beside it. |
| `docs/` | The one-page summary, and the notebook as a web page. |
| `.github/workflows/` | Runs the notebook from top to bottom on every push. |

## Checks and limits

The notebook stops on a broken checksum, duplicate or impossible fixings, or terms it does not implement. Known answers (Easter dates, the Easter lookback, Modified Following both ways) are tested in their own cell. Days with no fixing in the download get the supplement's fallback and are reported as exceptions to confirm, because a gap in today's data does not prove the rate was unavailable on the day. Fixings are as downloaded on 16 November 2026; revisions since first publication are not checked. The pricing uses a flat forward and an assumed base margin, so it is a scenario, not a fair value.

## Sources

- [SBSZ01 pricing supplement, 21 May 2025](https://clientportal.jse.co.za/Content/JSEPricingSupplementsItems/2025/SBSZ01%20PricingSupplement2605.pdf)
- [SBSA programme memorandum, 19 December 2024](https://www.standardbank.com/static_file/Investor%20Relations/Documents/Debt-investors/SBSA-Domestic-Medium-term-programme/SBSA-Programme-Memorandum_2024.pdf), condition 7.2(h)
- [SENS tap notice, 17 September 2025](https://senspdf.jse.co.za/documents/SENS_20250917_S510660.pdf)
- SARB web API, series MMRD855A, and the SARB's MPC statements
- Each row in `inputs/` names its own source.

Built by Ikaelelo (Ike) Dikhing with AI assistance for code and drafting; every figure was checked against the sources above. November 2026.
