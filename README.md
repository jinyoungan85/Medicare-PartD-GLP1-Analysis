# Medicare Part D GLP-1–Based Therapy Utilization and Spending Trends (2014–2024)

![Python](https://img.shields.io/badge/Python-3.x-blue)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-orange)
![CMS](https://img.shields.io/badge/Data-CMS%20Medicare%20Part%20D-green)
![Domain](https://img.shields.io/badge/Domain-Healthcare%20Analytics-teal)

## Project Overview

This project analyzes national and Georgia-level utilization and spending trends for selected single-agent GLP-1–based therapies in Medicare Part D from 2014 through 2024.

A reproducible Python/Pandas workflow processes more than 1.2 million rows across 11 annual CMS files, validates the data structure, defines the therapy cohort, engineers standardized utilization and cost metrics, and evaluates year-over-year spending drivers.

> **Scope note:** Tirzepatide is a dual GIP/GLP-1 receptor agonist and is included under the broader term **GLP-1–based therapies**. Insulin/GLP-1 combination products are excluded.

[View the full Jupyter Notebook](./Medicare_PartD_GLP1_Utilization_and_Spending_Trend_2014_2024.ipynb)

## Key Findings

- **Semaglutide led both utilization and spending in 2024**, with approximately **16.6 million standardized 30-day fills** and **$15.2 billion in aggregate gross drug cost**.

- **Semaglutide and tirzepatide were the largest positive contributors to the 2023–2024 increase in gross drug cost**, adding approximately **$4.30 billion** and **$3.97 billion**, respectively.

- For both leading cost drivers, standardized-fill growth exceeded gross-cost growth while gross cost per standardized 30-day fill declined:
  - Semaglutide: fills **+46.1%**, gross cost **+39.6%**, unit cost **−4.4%**
  - Tirzepatide: fills **+183.0%**, gross cost **+168.4%**, unit cost **−5.2%**

  This indicates that their 2024 spending increases were driven primarily by rapid utilization growth rather than higher gross cost per standardized fill.

- Weekly products became the dominant dosing-frequency group during the study period. Georgia followed the same overall direction as the national data, although the comparison is descriptive and is not adjusted for enrollment, demographics, disease prevalence, or benefit design.

- Liraglutide lost within-class utilization share following the introduction and expansion of semaglutide. Once-weekly dosing, stronger comparative weight-loss efficacy, and the availability of oral semaglutide are clinically plausible contributors, but the aggregated CMS data cannot establish their independent causal effects.

## Analytical Questions

1. How did national aggregate gross drug cost change from 2014 through 2024?
2. How did the utilization and spending mix shift among the included therapies?
3. What proportion of standardized 30-day fills came from weekly versus daily products?
4. How did gross cost per standardized 30-day fill change nationally and in Georgia?
5. Which therapies contributed most to the latest year-over-year change in gross drug cost?

## Data Source

**CMS Medicare Part D Prescribers — by Geography and Drug**

- [CMS dataset](https://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers/medicare-part-d-prescribers-by-geography-and-drug)
- [CMS data dictionary](https://data.cms.gov/resources/medicare-part-d-prescribers-by-geography-and-drug-data-dictionary)

The dataset is aggregated by geography and drug and includes prescription claims, standardized 30-day fills, gross drug cost, prescriber counts, and beneficiary counts.

Raw annual CSV files are not included in this repository because of their size.

## Cohort Definition

The analysis uses an exact generic-name whitelist to retain the following single-agent therapies:

- Albiglutide
- Dulaglutide
- Exenatide
- Exenatide extended release
- Liraglutide
- Lixisenatide
- Semaglutide
- Tirzepatide

Exact matching was used instead of partial text searches to prevent insulin/GLP-1 combination products from entering the cohort.

## Metric Definitions

### Standardized 30-Day Fills

`Tot_30day_Fills` is the primary utilization measure.

A single prescription claim may represent a 30-, 60-, or 90-day supply. Standardizing utilization into 30-day equivalents reduces variation caused by different days supplied and provides a more comparable estimate of therapy volume across products and years.

### Total Claims

`Tot_Clms` is retained as a secondary operational-volume measure. It represents dispensing events but may not reflect equivalent medication volume when days supplied differ.

### Gross Drug Cost

`Tot_Drug_Cst` represents aggregate reported gross drug cost, including amounts paid by Part D plans, beneficiaries, government subsidies, and other third-party payers.

It should **not** be interpreted as:

- Rebate-adjusted net PBM cost
- Plan liability
- Patient out-of-pocket cost
- Negotiated acquisition cost

### Cost per Standardized 30-Day Fill

`Cost_Per_30Day_Fill` is calculated as:

```text
Tot_Drug_Cst / Tot_30day_Fills
```

This metric provides a more comparable unit-cost measure than cost per claim because it partially accounts for differences in days supplied.

### Within-Class Shares

Utilization and spending shares are calculated only among the therapies included in this analysis. They are **within-class composition measures**, not estimates of total U.S. pharmaceutical market share.

## Analysis Workflow

1. **Batch ingestion**
   - Identifies annual CMS CSV files with `glob`
   - Extracts the data year from file names using regular expressions
   - Reads only required columns

2. **Schema and data validation**
   - Confirms required columns exist in every annual file
   - Reviews row counts, data types, missingness, and geographic coverage
   - Standardizes text fields before matching

3. **Cohort construction**
   - Applies an exact generic-name whitelist
   - Excludes insulin combination products
   - Maps generic names to consistent therapy labels

4. **Suppression-aware data handling**
   - Preserves missing beneficiary values rather than imputing them as zero
   - Excludes beneficiary counts from primary utilization and share calculations

5. **Geographic analysis**
   - Uses CMS national summary rows directly
   - Retains Georgia as a focused state comparison

6. **Metric engineering**
   - Aggregates claims, standardized fills, and gross drug cost
   - Calculates gross cost per claim and per standardized 30-day fill
   - Calculates within-class utilization and spending shares

7. **Dosing-frequency analysis**
   - Maps products to standard daily or weekly dosing
   - Performs an explicit unmapped-brand validation check
   - Compares weekly standardized-fill share nationally and in Georgia

8. **Year-over-year cost-driver analysis**
   - Calculates absolute and percentage changes in gross drug cost
   - Separates fill growth from unit-cost growth
   - Ranks positive cost contributors by absolute dollar change

## Visualizations

The notebook includes:

1. National utilization share by therapy
2. National aggregate gross drug cost
3. National spending share by therapy
4. Weekly standardized-fill share: Georgia versus national
5. Gross cost per standardized 30-day fill: Georgia versus national
6. Latest-year national cost-driver table

## Clinical and Healthcare Operational Context

The shift toward newer therapies occurred alongside meaningful differences in dosing frequency, route of administration, clinical evidence, indications, and market availability.

Injectable semaglutide is administered once weekly, whereas liraglutide is administered once daily. The STEP 8 randomized trial also reported substantially greater average weight loss with semaglutide 2.4 mg than with liraglutide 3.0 mg. Rybelsus provided an oral semaglutide option during the study period, potentially expanding route-of-administration choices.

These factors provide clinically plausible context for the observed product-mix shift, but they are **not causal findings from this dataset**.

From a healthcare-operations perspective, rapid utilization growth in high-cost therapies may increase the volume and complexity of:

- Prior authorization
- Clinical-documentation review
- Coverage verification
- Appeals
- Medication-access coordination
- Patient cost counseling
- Budget forecasting

Weekly dosing may reduce administration burden and could support persistence or adherence for some patients. However, this dataset does not contain patient-level dispensing histories or enrollment periods and therefore cannot calculate proportion of days covered, treatment persistence, HEDIS adherence performance, or confirmed medication use.

## Limitations

1. **Aggregated data:** The dataset is summarized by geography and drug rather than by patient. It cannot measure individual switching, persistence, adherence, outcomes, comorbidities, or treatment indication.

2. **Medicare Part D population:** Findings may not generalize to commercial insurance, Medicaid, uninsured populations, or younger patients.

3. **Beneficiary suppression:** Beneficiary counts below the CMS reporting threshold may be blank. Missing beneficiary values were not imputed as zero.

4. **Utilization proxy:** Standardized 30-day fills improve comparability but are not equivalent to unique patients, proportion of days covered, medication possession ratio, or confirmed use.

5. **Gross rather than net cost:** The dataset does not include rebates, manufacturer discounts, formulary terms, plan liability, or rebate-adjusted net cost.

6. **Nominal dollars:** Costs were not adjusted for inflation.

7. **Within-class denominator:** Utilization and spending shares include only the selected therapies and should not be interpreted as total-market shares.

8. **Geographic comparison:** Georgia and national comparisons are not adjusted for enrollment, demographics, disease prevalence, or local benefit design.

9. **Descriptive frequency analysis:** The relationship between weekly-product share and utilization does not prove that dosing convenience caused adoption.

10. **Clinical and market attribution:** The analysis cannot isolate the effects of efficacy, route, labeling, guidelines, formulary placement, rebates, shortages, promotion, or patient and prescriber preferences.

11. **Temporal coverage and publication lag:** The analysis ends in 2024 and does not capture subsequent product, guideline, labeling, or policy changes, including the 2026 U.S. availability of Wegovy tablets, the 2026 approval of oral orforglipron (Foundayo), or the Medicare GLP-1 Bridge beginning July 1, 2026. The Bridge operates outside the standard Part D coverage and payment flow, so its utilization and payments may not appear in this dataset in the same manner as ordinary Part D claims.

12. **Annual aggregation:** Events introduced during 2024 may affect only part of the calendar year, and annual totals cannot isolate their timing or independent effects.

## Reproducibility

### Requirements

```bash
pip install pandas numpy matplotlib seaborn jupyter
```

### Run the Analysis

1. Clone this repository.
2. Download the 2014–2024 annual CMS CSV files from the dataset page.
3. Create a folder named `Raw Data CSV` in the project root.
4. Place all 11 annual CSV files in that folder.
5. Open the notebook:

```text
Medicare_PartD_GLP1_Utilization_and_Spending_Trend_2014_2024.ipynb
```

6. Restart the kernel and run all cells.
7. Confirm that:
   - 11 annual files were loaded
   - The year range is 2014–2024
   - No insulin combination products remain
   - All included brands have a reviewed dosing-frequency mapping

## Repository Structure

```text
.
├── Medicare_PartD_GLP1_Utilization_and_Spending_Trend_2014_2024.ipynb
├── README.md
└── Raw Data CSV/        # Local only; excluded from GitHub
```

## Tools and Skills Demonstrated

- Python
- Pandas and NumPy
- Matplotlib and Seaborn
- Batch file processing
- Schema validation
- Data cleaning and cohort construction
- Missing-data and suppression handling
- Metric engineering
- Trend analysis
- Year-over-year cost-driver analysis
- Healthcare and pharmacy-domain interpretation
- Reproducible analytical documentation

## Selected Clinical and Policy References

- [STEP 8 randomized clinical trial: semaglutide versus liraglutide](https://doi.org/10.1001/jama.2021.23619)
- [FDA: Wegovy cardiovascular-risk indication](https://www.fda.gov/news-events/press-announcements/fda-approves-first-treatment-reduce-risk-serious-heart-problems-specifically-adults-obesity-or)
- [FDA: Foundayo approval](https://www.fda.gov/news-events/press-announcements/fda-approves-first-new-molecular-entity-under-national-priority-voucher-program)
- [CMS: Medicare GLP-1 Bridge](https://www.cms.gov/medicare/coverage/prescription-drug-coverage/medicare-glp-1-bridge)
