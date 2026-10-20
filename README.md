# Wind Farm Siting Analysis

A techno-economic analysis of where a utility should site ten new 200 MW merchant wind farms across the U.S., using county-level turbine siting characteristics and hourly 2023 wholesale electricity price/production data (26.4M hourly records, 3,018 counties).

**[View the full report](https://usama1208.github.io/wind-farm-siting-analysis/)**

## What's here
- `wind_farm_siting_analysis.Rmd` — full R Markdown source: data cleaning, revenue/cost/NPV modeling, policy-uncertainty (PTC) scenarios, Monte Carlo robustness checks, and portfolio diversification analysis.
- `index.html` — rendered report with all tables, figures, and the final 10-county recommendation (same content as the link above).
- `RESULTS_SUMMARY.md` — one-page summary of headline findings.
- `CHANGELOG.md` — log of data-quality issues found and fixed, and methodology decisions.
- `CountyChars.csv` — county-level characteristics (turbine siting factors, demographics, transmission distance, etc.).

**Note:** the hourly production/price panel (`ProdAndPrice.csv`, ~774MB, 26.4M rows) that this analysis also relies on is excluded here due to GitHub's 100MB file size limit.

## Headline finding
At 2023 wholesale prices, none of the 3,009 evaluable counties produce a positive NPV for a standalone merchant wind farm — even with the full federal Production Tax Credit reinstated. The recommended sites are driven by captured price (value factor), not raw capacity factor, which favors coastal New England/Mid-Atlantic/Great Lakes counties over the classic Great Plains wind corridor.
