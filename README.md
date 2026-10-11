# US County Emissions 2023 – Power BI Project

A Power BI report and star-schema semantic model analysing 2023 energy consumption and greenhouse gas (GHG) emissions across 3,142 US counties.

Stored as a **Power BI Project (PBIP)** so the model (TMDL) and report (PBIR) are plain text and diff-friendly in Git.

## What's in it

**Semantic model**

| Table | Role | Grain |
|---|---|---|
| Emissions | Fact | One row per county |
| County | Dimension | County, state, coordinates, consolidated city-county flag |
| Climate Zone | Dimension | DOE climate zones 1–8 |
| Population Cohort | Dimension | Cohorts 1–20 |
| Employment Cohort | Dimension | Cohorts 1–20 |
| Date | Dimension | Daily calendar, 2022-01-01 to 2024-12-31 |

- All relationships are many-to-one, single direction, from `Emissions`.
- Source CSV has duplicate, sector-less headers, so columns are renamed by position in Power Query. Thousands separators are parsed, and per-capita and empty columns are dropped (per-capita is calculated by measures).
- Sort-by columns are set for month, quarter, day of week, zone, and cohort labels. Keys have no default summarisation, and base metric columns are hidden behind measures.
- The CSV path is a single parameter, `Source File Path`.
- 46 DAX measures in two display folders:
  - **Core Measures**: Demographics, Emissions, Electricity, Natural Gas, Transportation
  - **Time Intelligence**: YTD, QTD, MTD, prior year, prior-year YTD, YoY change, YoY %, rolling 12 months

**Report (5 pages)**

1. **Overview** – KPI cards, GHG treemap by state, Top 10 states, electricity by sector and climate zone
2. **Geography** – county scatter (longitude/latitude, sized by GHG), Top 10 counties, state/county matrix
3. **Energy Mix** – electricity and natural gas by sector, residential electricity per capita vs population
4. **Transportation** – gasoline, diesel and vehicle miles traveled; miles per capita by population cohort
5. **County Profile** – hidden drill-through page (right-click a county → Drillthrough)

Custom theme, consistent sector colours, and State / Climate Zone / Population Cohort slicers synced across pages.

## Run it

1. Open `Youtube_Tutorial.pbip` in Power BI Desktop (recent version with PBIP/PBIR support).
2. Open **Transform data → Manage parameters** and set `Source File Path` to where `Emissions_Data_2023.csv` lives on your machine.
3. Refresh.

## Known limitations

- **Sector labels are inferred.** The source columns don't name their sectors, so residential / commercial / industrial labels come from column order and magnitudes. Treat them as unconfirmed until checked against a data dictionary. The two "Commercial … Utility" blocks are the least certain and are left out of the electricity and natural gas totals to avoid possible double counting.
- **Expenditure units are unverified.** Headers say "Millions" but values look like thousands of dollars, so they are formatted as plain numbers.
- **Single-year snapshot.** There is no date column. The fact table carries a constant reporting date (31 Dec 2023), so it joins to the Date table. Prior-period measures return blank until more years are loaded.
- **No Azure Maps.** Maps need a sign-in in Desktop, so geography uses a treemap and a lat/long scatter.
- County Profile cards show truncated totals when opened without a drill-through selection.

## Data

`Emissions_Data_2023.csv`: county-level 2023 energy, transportation, and GHG figures. Add the original source and licence here before publishing.

## Built with

Power BI Desktop, DAX, Power Query (M). The model and report were authored with AI assistance (Claude Code with Power BI modelling and report-authoring tooling) and reviewed page by page.
