# Time-Series Analysis of Danish School Well-Being Measurements

This repository contains an R Markdown analysis of whether the Danish national school well-being measurement (`trivselsmålingen`) is associated with changes in the demand for soft competencies in Danish public-school teacher job postings.

The main analysis is implemented in [`src/script.Rmd`](src/script.Rmd). It cleans and filters job-posting data, identifies public schools and municipalities, counts competency mentions with a dictionary method, and estimates interrupted time-series models around the first well-being measurement on `2015-06-01`.

## Repository Structure

```text
.
├── datasets/
│   ├── laerer_test_renset.csv
│   ├── jobs_teacher.csv
│   ├── Samletkodeliste-udenaar.xlsx
│   ├── Folkeskoler.xlsx
│   ├── Ikke-danske_lokationer.xlsx
│   ├── kommune_missing.xlsx
│   ├── kommune_postnumre.xlsx
│   └── by_kommune.xlsx
├── src/
│   ├── script.Rmd
│   ├── data_cleaning.ipynb
│   ├── ITS_samtlige_skoler.html
│   ├── ITS_samtlige_skoler_inklusionsloven.html
│   └── ITS_samtlige_skoler_reformen.html
├── utils/images/
│   ├── kommune_fra_by.png
│   ├── kommune_hjemmeside.png
│   ├── kommune_kolonne.png
│   └── ordbogsmetoden.png
├── LICENSE
└── README.md
```

## Analysis Overview

The R Markdown workflow has four main stages:

1. **Data preparation**

   The notebook loads the cleaned teacher job-posting data from `datasets/laerer_test_renset.csv`, removes irrelevant columns, filters invalid postings, restricts the sample to teacher postings from `2007` through `2022`, excludes non-Danish postings, and narrows the data to public-school job postings.

2. **School and municipality identification**

   School names are identified with a combination of register matching from `datasets/Folkeskoler.xlsx` and regular expressions across posting text, title, and workplace fields. Municipality names are assembled from several sources, including text matching, original municipality fields, website fields, manually coded missing values, and a city-to-municipality lookup.

3. **Competency measurement**

   The notebook reads `datasets/Samletkodeliste-udenaar.xlsx` and builds a dictionary of 11 soft-competency categories. Each posting is marked for whether it contains a match for each competency, and the total number of matched competencies is summed per posting.

4. **Interrupted time-series analysis**

   The analysis estimates models before and after `2015-06-01`, using:

   - `Y`: total number of matched competencies.
   - `T`: time index.
   - `D`: indicator for dates after the well-being measurement.
   - `P`: time elapsed after the well-being measurement.

   The notebook includes an aggregate model for all schools, school-level models for schools with enough observations before and after the intervention, seasonal plots, outlier checks, lagged models, Durbin-Watson tests for autocorrelation, and selected school-level plots.

## Requirements

The project is written for R and R Markdown. It was inspected locally with R `4.5.2`, but the code should work with a recent R 4.x installation if the package versions are compatible.

Required R packages:

```r
install.packages(c(
  "rmarkdown",
  "readr",
  "tidyverse",
  "readxl",
  "lubridate",
  "stringr",
  "quanteda",
  "data.table",
  "broom",
  "nlme",
  "ggplot2",
  "stargazer",
  "fpp2",
  "lmtest"
))
```

## How to Run

### RStudio

Open `src/script.Rmd` in RStudio and knit the document to HTML.


## Expected Outputs


The repository also contains separate HTML result tables for selected aggregate interrupted time-series models. These tables are generated with `stargazer` and report the dependent variable as the change in soft competencies for all schools.

| File | Description |
| --- | --- |
| `src/ITS_samtlige_skoler.html` | Aggregate ITS model for all schools around the first well-being measurement. |
| `src/ITS_samtlige_skoler_reformen.html` | Aggregate ITS model for all schools using the school reform as the intervention point. |
| `src/ITS_samtlige_skoler_inklusionsloven.html` | Aggregate ITS model for all schools using the inclusion law as the intervention point. |

## Result Tables

The three saved result tables contain comparable aggregate models with `4,175` observations each:

| Result table | Pre-intervention trend | Immediate level change | Post-intervention trend | R-squared |
| ------------------------------------------ | -----------: | -----------: | ------------: | --------: |
| `ITS_samtlige_skoler.html`                 | `0.00061***` | `0.71128***` | `-0.00026***` | `0.39638` |
| `ITS_samtlige_skoler_reformen.html`        | `0.00048***` | `0.91582***` | `-0.00008`    | `0.40103` |
| `ITS_samtlige_skoler_inklusionsloven.html` | `-0.00006`   | `1.13417***` | `0.00064***`  | `0.40560` |

Significance markers follow the `stargazer` convention used in the files: `* p < 0.1`, `** p < 0.05`, and `*** p < 0.01`.

## Data Inputs

The analysis depends on the datasets in `datasets/`:

| File | Role |
| --- | --- |
| `laerer_test_renset.csv` | Main cleaned job-posting dataset used by `script.Rmd`. |
| `jobs_teacher.csv` | Additional teacher job-posting dataset stored in the repository. |
| `Samletkodeliste-udenaar.xlsx` | Dictionary terms for the soft-competency categories. |
| `Folkeskoler.xlsx` | Public-school register used to identify school names. |
| `Ikke-danske_lokationer.xlsx` | Locations used to filter non-Danish postings. |
| `kommune_missing.xlsx` | Manually identified municipalities for postings with missing municipality matches. |
| `kommune_postnumre.xlsx` | Municipality and postal-code lookup. |
| `by_kommune.xlsx` | City-to-municipality lookup used to reduce unmatched municipality names. |

## License

This project is licensed under the MIT License. See [`LICENSE`](LICENSE) for details.
