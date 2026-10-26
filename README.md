# Predicting National CO₂ Emissions from WDI

**DS 3001 semester project** | World Bank original-source data; no Kaggle data.

## Research question
How accurately can economic and energy indicators from year `t` predict a country’s CO₂ emissions per capita in year `t+1`?

## Quick start

```bash
cd project
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/final_analysis.ipynb
```
Run **Kernel → Restart & Run All**. Internet access is required on the first run. Subsequent runs use cached CSVs. To refresh, delete the relevant CSV(s) under `data/raw/world_bank_indicators/`. The final notebook stores its figures in `figures/` and processed output in `data/processed/`.

## Indicator codes (World Bank WDI)

| Column | Code | Meaning |
|---|---|---|
| `co2_pc` | `EN.GHG.CO2.PC.CE.AR5` | CO₂ emissions per capita (excluding LULUCF) |
| `gdp_pc` | `NY.GDP.PCAP.KD` | GDP per capita, constant currency |
| `fossil_pct` | `EG.USE.COMM.FO.ZS` | Fossil fuel consumption (% total) |
| `renewable_pct` | `EG.FEC.RNEW.ZS` | Renewables (% final consumption) |
| `energy_intensity` | `EG.EGY.PRIM.PP.KD` | Primary energy intensity |
| `industry_pct` | `NV.IND.TOTL.ZS` | Industry value added (% GDP) |
| `urban_pct` | `SP.URB.TOTL.IN.ZS` | Urban population (%) |

Source: https://databank.worldbank.org/source/world-development-indicators  
API: https://api.worldbank.org/v2/  
Indicator definitions: https://data.worldbank.org/indicator/EN.GHG.CO2.PC.CE.AR5

## Data provenance and preparation

1. Fetch each series separately from original World Bank API, using `source=2`, years 2000–2020, and explicit pagination.
2. Fetch country metadata and remove regional/income aggregates.
3. Check duplicates and invalid numeric/percentage values.
4. Left-join individual indicator series on `(country_code, year)`.
5. Shift the emissions measurement **one calendar year** using an explicit year-key join.
6. Remove missing targets; retain missing predictors for training-only imputation.
7. Compare chronological train (2000–2014), validation (2015–2017), and test (2018–2019) predictor years.

## Outputs

- `data/raw/world_bank_indicators/*.csv`: original downloaded series and country metadata.
- `data/processed/modeling_dataset.csv`: full merged training table (including missing predictors).
- `data/processed/test_metrics.csv` and `test_predictions.csv`: final held-out evaluation.
- `figures/*.png`: EDA and final comparison charts.