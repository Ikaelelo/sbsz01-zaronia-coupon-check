# SBSZ01: checking a ZARONIA note's coupons from first principles

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Ikaelelo/sbsz01-zaronia-coupon-check/blob/main/SBSZ01_Colab.ipynb)

Standard Bank's SBSZ01 is a listed floating-rate note paying compounded ZARONIA plus 1.02%. This notebook rebuilds every coupon it has paid from the SARB's daily ZARONIA fixings and the terms of the pricing supplement, checks each one against the issuer's SENS notices (the rate and the rand amount), and values the note at different credit spreads.

**To run it:** click the badge above, then choose Runtime → Run all in Colab. The notebook fetches its input files from this repository and asks the SARB for live fixings, falling back to a saved copy of the SARB's reply if the API cannot be reached. It needs only standard Python.

## Results

| Payment | Calculated | SENS | Cash at the calculated rate |
| --- | --- | --- | --- |
| 26 Aug 2025 | 8.1713% | 8.1713% | Ties to the cent on R756m (R15 570 691.99) |
| 26 Nov 2025 | 7.9343% | rate not read | Ties to the cent on R1.796bn (R35 917 815.28) |
| 26 Feb 2026 | 7.6815% | notice not read | calculated only |
| 26 May 2026 | 7.6772% | 7.677% | R875.86 more than paid; the cash ties to the printed 7.677% |
| 26 Aug 2026 | 7.9058% | notice not read | calculated only |

Plausible wrong conventions (a lookback one day out, an observation shift, a simple average) all fail to reproduce both 2025 amounts. Just after the May 2026 coupon, the note prices at R100 at the 102 bp contractual margin, R99.54 at +25 bp and R99.09 at +50 bp (spread PV01 about R0.0184 per R100). The pricing is a scenario with a flat forward, not a fair value.

## What is where

| Path | What it holds |
| --- | --- |
| `SBSZ01_Colab.ipynb` | The method: calendar, coupon engine, checks, pricing. Saved with its outputs. |
| `inputs/sbsz01_terms.json` | The note's terms, each with its clause in the pricing supplement. |
| `inputs/sens_notices.csv` | The issuer's SENS notices: rate as printed, interest paid, source. |
| `inputs/nominal_history.csv` | The amount in issue over time, from the issuer's tap notice. |
| `inputs/repo_rate_changes.csv` | SARB repo rate decisions (history chart only). |
| `inputs/declared_holidays.csv` | One-off public holidays; regular ones, Easter included, are calculated by rule. |
| `data/sarb_MMRD855A_cache.json` | The SARB API's reply saved byte for byte, with its SHA-256 in the `.meta.json` beside it. |
| `docs/` | A two-page summary, an eight-slide deck, and the notebook as a web page. |

## Sources

- [SBSZ01 pricing supplement, 21 May 2025](https://clientportal.jse.co.za/Content/JSEPricingSupplementsItems/2025/SBSZ01%20PricingSupplement2605.pdf)
- [SBSA programme memorandum, 19 December 2024](https://www.standardbank.com/static_file/Investor%20Relations/Documents/Debt-investors/SBSA-Domestic-Medium-term-programme/SBSA-Programme-Memorandum_2024.pdf), condition 7.2(h)
- [SENS tap notice, 17 September 2025](https://senspdf.jse.co.za/documents/SENS_20250917_S510660.pdf)
- SARB web API, series MMRD855A, and the SARB's MPC statements
- Each row in `inputs/` names its own source.

Ikaelelo (Ike) Dikhing, November 2026.
