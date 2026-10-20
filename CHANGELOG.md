# Changelog

## Data issues found and fixed
- **Dropped 7 counties with no price data.** FIPS 12081, 12091, 12103, 12131 (FL),
  25007 (Dukes Co., MA), 37053 (Hyde Co., NC), and 53053 (Pierce Co., WA) have
  `Price = NA` for all 8,760 hours even though production data is normal for them.
  A naive `sum()` would silently return `NA` revenue for these counties, which
  breaks downstream ranking without any warning. They're now explicitly excluded
  before any revenue/cost/NPV calculation.
- **Reframed the curtailment analysis around the real $0 price floor.** Prices in
  the data never go negative — they floor at exactly $0/MWh for about 4% of
  county-hours. Since $0 × any output is still $0, curtailing production during
  these hours doesn't lose revenue, but it does avoid a few dollars of O&M cost
  per MWh, so curtailing at the floor is weakly valuable even without a
  production tax credit (PTC), and clearly valuable with one.
- **Fixed a `maps::map()` / `purrr::map()` namespace collision** in the cluster
  sensitivity analysis — `maps` was loaded after `tidyverse`, so `map()` resolved
  to the geographic-mapping function instead of `purrr::map()` and threw an
  unrelated error. Fixed by calling `purrr::map()` explicitly.
- **Fixed a pandoc rendering bug that was silently corrupting several HTML
  tables.** Pandoc's default math-parsing rules treat any two `$` signs in the
  same block as a TeX math delimiter pair. Dollar signs in adjacent table-header
  text (e.g., "Revenue ($M/yr)" and "Capture price ($/MWh)") were being paired up
  this way, which swallowed the HTML tag between them. Fixed by disabling
  dollar-sign math parsing and converting the report's actual math expressions to
  `\(...\)` / `\[...\]` syntax instead.
- Fixed a double-scaling bug in the executive summary that was dividing an
  already-scaled dollar figure by another million, and corrected several stale
  section cross-references left over from reorganizing the report.

## Analysis added
- County names added to every county-level table.
- Interconnection/transmission cost assumptions grounded in MISO's transmission
  cost guide and LBNL's interconnection-cost-trends data.
- Permitting-risk discussion calibrated against the Columbia Sabin Center's count
  of local wind restrictions.
- Recovered 135 distinct wholesale balancing areas directly from the hourly price
  data (by matching counties on their 8,760-hour price signature) and reran the
  cannibalization analysis at this finer geography instead of by state.
- Added a "placed in service by 2027" PTC scenario alongside the political-
  reinstatement scenario, and combined them into a single expected-value PTC
  probability.
- Added a 500-draw Monte Carlo robustness check across discount rate, capex,
  transmission cost, price trend, and PTC status.
- Added a sensitivity check on the portfolio diversification thresholds
  (max farms per state / balancing area / correlation limit).
- Added an executive summary section.

## Known limitations
- Revenue estimates rest on a single weather year (2014 production) and a single
  price year (2023) — multi-year data would be the most valuable extension.
- The units of the `count_houses`/`sum_prices` distance-band variables (miles vs.
  km) aren't documented; the analysis assumes miles, consistent with the
  property-value literature it cites, but this affects the permitting cutoff and
  property-value-loss estimate if wrong.
- Interconnection-queue network-upgrade costs aren't in the data and can be
  large; they're discussed qualitatively but not included in the per-county
  ranking.
- Permitting probabilities are stylized, not calibrated against an actual
  ordinance database.
