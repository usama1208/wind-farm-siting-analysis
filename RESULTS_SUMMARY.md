# Wind Farm Siting — Results Summary

## Headline finding
Under 2023 wholesale prices, today's costs, and no production tax credit (PTC),
**none of the 3,009 evaluable counties has a positive NPV** for a standalone 200 MW
merchant wind farm (6% real discount rate, 25-year life). This holds even with the
full PTC reinstated (**0 of 3,009 counties become NPV-positive**) — the credit is
worth real money but not enough to flip the sign at 2023 prices. The honest framing
for NextEra is *least-bad, most option-creating* siting, not ten slam-dunk projects.

## The 10 recommended counties (diversified, risk-adjusted for permitting)

| Rank | FIPS | County, State | Gen (GWh/yr) | CF | Revenue ($M/yr) | Fixed cost ($M) | NPV ($M) | Break-even ($/MWh) | Robust (of 15 scenarios) | Top-10 w/ PTC? |
|---|---|---|---|---|---|---|---|---|---|---|
| 1 | 36093 | Schenectady, NY | 628.1 | 35.9% | 24.3 | 397.9 | **-227.2** | 66.9 | 15 | Yes |
| 2 | 9001 | Fairfield, CT | 565.5 | 32.3% | 21.9 | 398.9 | **-245.9** | 72.8 | 15 | Yes |
| 3 | 44001 | Bristol, RI | 514.0 | 29.3% | 21.3 | 396.8 | **-248.8** | 79.3 | 12 | No |
| 4 | 48113 | Dallas, TX | 615.4 | 35.1% | 19.2 | 397.7 | **-279.9** | 66.8 | 0 | No |
| 5 | 33017 | Strafford, NH | 469.2 | 26.8% | 18.4 | 403.1 | **-283.8** | 86.4 | 0 | No |
| 6 | 26125 | Oakland, MI | 543.4 | 31.0% | 18.8 | 398.9 | **-286.0** | 75.8 | 0 | No |
| 7 | 55133 | Waukesha, WI | 586.6 | 33.5% | 19.4 | 401.5 | **-290.2** | 71.8 | 0 | No |
| 8 | 51199 | York, VA | 495.0 | 28.3% | 17.6 | 399.7 | **-295.6** | 82.2 | 0 | No |
| 9 | 39085 | Lake, OH | 520.9 | 29.7% | 17.7 | 400.7 | **-296.5** | 78.6 | 0 | No |
| 10 | 50025 | Windham, VT | 674.7 | 38.5% | 24.5 | 411.3 | **-243.3** | 64.5 | 15 | Yes |

**Portfolio totals:** 10 states, 5,613 GWh/yr, $203.1M/yr revenue, $4,007M up-front
investment. Combined baseline NPV **-$2,697M** (0 of 10 individually positive).
Average break-even captured price **$74.5/MWh** — any PPA signed above that price
(plausible; many corporate/utility wind PPAs clear in that range) makes the farm
profitable. Average robustness: recommended counties stay in the top ten in **5.7 of
15** one-at-a-time sensitivity scenarios. **3 of 10** would also be top-ten picks if
the PTC returned.

Winners are mostly **coastal New England / Mid-Atlantic / Great Lakes** counties with
good-but-not-exceptional capacity factors (27%-39%) but high captured prices, close to
existing transmission — *not* the classic Great Plains wind corridor. The PTC
scenario's top ten skew toward the Great Plains instead (rewards raw MWh, not value).

## Profitability counts
- Baseline (no PTC): **0 of 3,009** counties NPV-positive.
- Full PTC ($26/MWh, 10 yrs): **0 of 3,009** counties NPV-positive.
- 500-draw joint Monte Carlo (discount rate, capex, transmission cost, price trend,
  PTC status all drawn together): **0% of counties profitable on average**; only
  **11 counties** land in the top ten in at least half of draws — the "safe" list is
  short and dominated by the same New England/coastal counties above.

## Key sensitivities (Table 6 / Table 7 / Table 12a)
- Shifting **capex, O&M, property tax, or the discount rate** moves NPV *levels* a lot
  but barely reorders counties (the ranking is robust to these).
- **Transmission cost per km**, the **PTC**, and **RECs** reshuffle the list the most,
  because they hit counties unevenly (PTC rewards high-CF Plains sites; RECs reward
  RPS states; transmission cost rewards grid-adjacent sites).
- The variance of PV revenue across counties is far larger than the variance of PV
  cost — **location choice is mostly a revenue decision**, not a cost decision.
- The diversification rule (max 2 farms/state, max 2/balancing area, output
  correlation < 0.8) is **not sensitive** to the exact thresholds chosen — tightening
  or loosening it changes expected portfolio NPV by only a few percent.

## Things to double-check before this goes out
1. **Revenue rests on one weather year (2014) and one price year (2023).** A calm
   2014 or an unusual 2023 price year would reorder counties; multi-year wind/price
   data would be the single biggest improvement.
2. **Units of `count_houses1`/`sum_prices1-5` (miles vs. km) are not confirmed** in
   the data documentation — we assumed miles (matches the LBNL property-value
   literature cited), but this affects the permitting cutoff and Table 11's
   property-value-loss estimate. Flagged as Limitations item 7.
3. **Interconnection-queue network-upgrade costs are not in the data** and can be
   large (LBNL: avg $194/kW completed, up to $671/kW for projects that withdraw) —
   not county-specific, so not in our ranking, but material to real-world due
   diligence on the final 10.
4. **Permitting probabilities (80% base, ±10/-15 points) are stylized**, not
   calibrated — NREL/OEDI's 2025 county-level wind ordinance database would sharpen
   this but was not pulled in (see "Declined/open proposals" in CHANGELOG.md).
5. Seven counties (FIPS 12081, 12091, 12103, 12131, 25007, 37053, 53053) have no
   price data for any hour of 2023 and are excluded entirely — worth flagging to
   students as a real, not cosmetic, data gap (e.g. Martha's Vineyard, MA).
