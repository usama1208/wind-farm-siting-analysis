# Wind Farm Siting — Changelog

All changes below are to `wind_farm_siting_analysis.Rmd`, verified against the real
`CountyChars.csv` and `ProdAndPrice.csv` and against the knitted output, not assumed.

## Bugs fixed (no sign-off needed — these made the draft wrong, not just incomplete)

1. **Dropped 7 counties with no price data at all.** FIPS 12081, 12091, 12103, 12131
   (FL), 25007 (Dukes Co./Martha's Vineyard, MA), 37053 (Hyde Co., NC), 53053 (Pierce
   Co., WA) have `Price = NA` for all 8,760 hours (production is normal). The original
   code's plain `sum()` would have silently produced `NA` revenue for them, breaking
   `slice_max()`/ranking downstream with no warning. Now explicitly dropped before any
   revenue/cost/NPV calculation, with the FIPS list and reason stated in the text.
2. **Reframed the "negative prices" discussion around the real $0 floor.** The draft
   assumed curtailment logic around P < 0. The real data never goes negative — price
   floors at exactly $0/MWh for ~4% of county-hours. Rewrote the section: since $0 ×
   any output is still $0, the "always produce" revenue baseline was already correct;
   what actually matters is that running turbines through a zero-revenue hour still
   costs a few dollars of O&M for nothing, so curtailing at the $0 floor is weakly
   valuable even without a PTC, and clearly valuable with one.
3. **`maps::map()` was silently masking `purrr::map()`** in the cluster-sensitivity
   chunk (both packages are loaded in `setup`, `maps` second). `map(fips_set,
   portfolio_stats)` resolved to the geographic-map function and errored on
   `database type not supported`. Fixed by calling `purrr::map()` explicitly; checked
   there are no other bare `map()` calls in the document.
4. **Knitr inline code can't contain backtick-quoted column names.** Writing
   `` `r sum(mc_summary\`P(top 10)\` >= 0.5)` `` inline broke the parser. Fixed by
   precomputing plain-named variables (`n_mc_top10_half`, `pct_profitable_mc`) at the
   end of the `monte-carlo` chunk and referencing those inline instead.
5. **Section-renumbering cascade.** Inserting the new Executive Summary and Monte
   Carlo Robustness sections shifted every subsequent `number_sections` auto-number.
   Manually re-mapped all 13 top-level sections and fixed ~14 hardcoded "Section N"
   text cross-references throughout the document; verified against the actual
   rendered `header-section-number` spans in the output HTML (exact match).
6. **Executive Summary NPV range was silently double-scaled to ~$0.** `usd_m()`
   already divides by 1e6 internally; the exec-summary chunk also divided by 1e6
   before calling it, rendering "-$0.0M to -$0.0M" instead of the real "-$227.2M to
   -$288.2M." Fixed by removing the redundant division.
7. **Stale cross-reference in Sources.** "Not yet incorporated here (see Sections 9
   and limitations)" pointed at the Monte Carlo section; the NREL/OEDI wind-ordinance
   database is actually discussed in Section 11 (Local Permitting). Fixed.
8. **Figure 5's caption said the data's time zone was "not stated," without using the
   evidence we already had.** Averaging price/output by `hour %% 24` across ~3,000
   counties nationally gives one sharp peak (hour 17-18), not a smeared one — which is
   only possible if each balancing area's `hour` is already indexed to *its own local
   standard time*, not a single national clock (UTC would blur the peak by the ~3-hour
   spread between US time zones). Added this inference as an explicit note; it does
   not change any revenue number (always computed per-county on that county's own
   8,760-hour series), only the interpretation of the national-average figure.
9. **Missing limitation: units of `count_houses1`/`sum_prices1-5` are not confirmed**
   (miles vs. km). We keep the draft's mile-based assumption (consistent with the LBNL
   property-value literature cited for the externality estimate) but added it as
   Limitations item 7, since our own check — comparing implied population density
   against 1-mile vs. 1-km circles — was inconclusive (turbines sit in the sparsest
   part of a county, not spread uniformly), so this is a real open question, not
   resolved.
10. **Pandoc was mis-parsing literal "$" currency signs as TeX math, corrupting
    several tables' HTML.** Pandoc's default `tex_math_dollars` extension pairs up any
    two `$` signs that satisfy its open/close rules. Table headers like "Revenue
    ($M/yr)" paired with "Capture price ($/MWh)" elsewhere in the same header row,
    and pandoc literally swallowed the `</th>` tag between them into an (invisible,
    broken) math span — corrupting Tables 2, 3, 5, 8, and 13's headers/captions and
    one Sources bullet. This was present in the original draft and only surfaced once
    county names made the tables large enough, and once I specifically diffed the
    rendered HTML against its source, to notice the broken tags. **Fix:** disabled
    `tex_math_dollars` and enabled `tex_math_single_backslash` via `md_extensions` in
    the YAML header, and converted the document's ~12 genuine math expressions (the
    NPV/cost formulas, $p$, $p_{2027}$, $p_{reinstate}$, $p_{PTC}$) from `$...$`/`$$...$$`
    to `\(...\)`/`\[...\]`, which is what the existing MathJax config block already
    expected. This removes the bug for every current and future literal "$" in the
    document, not just the ones already found.

## Approved improvements implemented ("do all," all 9 ranked proposals)

1. **County names** added to every county-level table (5, 7, 8, 11, 13) via
   `maps::county.fips`, with the 4 Virginia independent cities not in that lookup
   (Fairfax City, Franklin City, Richmond City, Roanoke City FIPS 51600/51620/51760/
   51770) patched in by hand and flagged as also missing from every county map figure.
2. **Interconnection/transmission cost citations** sharpened with real figures: MISO's
   *Transmission Cost Estimation Guide* for the $/mile spur-line assumption; LBNL's
   interconnection-cost-trends studies ($194/kW avg for completed land-based wind,
   $671/kW for withdrawn projects) added to the "costs we leave out" discussion and to
   Limitations.
3. **Permitting calibration text** sharpened with Columbia Sabin Center's restriction
   count (888 as of the 2026 report, up from 459 at end-2024) and an explicit flag
   that NREL/OEDI's 2025 county-level wind-ordinance database would calibrate the
   stylized probabilities better, but was not pulled in (see "Declined/open" below).
4. **Balancing-area cannibalization analysis.** Recovered 135 distinct wholesale
   balancing areas by grouping counties on a signature of their 8,760-hour price
   series (sum, sd, 3 spot-check hours) — a much finer, more economically correct unit
   than the 48 states the draft originally used. Reran the value-factor-vs.-existing-
   wind regression at the balancing-area level (Table 10) and added balancing area as
   a third diversification constraint (alongside state and output correlation) in the
   final portfolio selection.
5. **PTC "placed in service by 2027" scenario.** Added explicit treatment of the
   OBBBA's construction-timeline provision as a separate probability channel from
   political reinstatement, combined as
   \(p_{PTC} = p_{2027} + (1-p_{2027}) \times p_{reinstate}\) (Table 9a), carried into
   the existing expected-value PTC analysis (Table 9).
6. **Monte Carlo robustness check** (new section, Table 7b): 500 joint draws over
   discount rate, capex, transmission cost, price trend, and PTC status, recomputing
   NPV each time. Tabulated efficiently via `table(unlist(...))` rather than per-draw
   `%in%` lookups.
7. **Cluster-vs-spread threshold sensitivity** (Table 12a): reran the diversified
   portfolio selection at 5 different (max/state, max/balancing-area, max-correlation)
   settings to show the recommendation is not an artifact of the specific thresholds
   chosen.
8. **Executive Summary** added as the new first section, via a duplicated condensed
   pipeline in an early `include=FALSE` chunk (dot-prefixed throwaway variable names
   to avoid colliding with the later pedagogical chunks). This is a real, documented
   maintenance cost — the ~15 lines of core arithmetic now exist twice — accepted as
   worthwhile for the deliverable.
9. **Full narrative re-read against real results**, correcting every place the prose
   assumed an outcome the data didn't support (see bugs #5-9 above, which were found
   during this pass) and confirming every remaining "Section N" cross-reference,
   geography claim (e.g. "coastal New England, not the Great Plains, leads the no-PTC
   ranking"), and headline number against the actual knitted output rather than
   assumption.

## Declined / open proposals (not implemented — flagged in the report itself)
- **NREL/OEDI's 2025 county-level wind siting ordinance/zoning database** would
  calibrate the permitting-probability model with real restriction flags instead of
  `count_houses1` as a proxy. Not pulled in because it is a new, large external
  dataset and downloading new data without sign-off was out of scope; flagged in
  Section 11 and in Limitations item 4 as the natural next step.
- **Multi-year wind/price data** (NREL WIND Toolkit 2007-2013, Cambium forward price
  scenarios) would address the "one weather year, one price year" limitation; not
  pulled in for the same reason (new, large external datasets).
