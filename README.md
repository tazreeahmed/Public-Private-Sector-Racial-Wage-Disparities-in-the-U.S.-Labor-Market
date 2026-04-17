# Public-Private Sector Racial Wage Disparities in the U.S. Labor Market

Regression analysis of racial and ethnic wage disparities across public and private sector employment using CPS microdata (2000–2023). Implements a Mincer earnings regression to isolate the wage gap by race after controlling for human capital, demographic, and structural factors.

---

## Dataset

- Source: Current Population Survey (CPS) ASEC microdata, 2000–2023
- Unit of observation: Working-age adults (18–65) with positive wages, hours, and weeks worked
- Final sample size: varies by year after cleaning
- Key variables: INCWAGE, UHRSWORKT, WKSWORK1, RACE, HISPAN, EDUC, AGE, SEX, MARST, NATIVITY, CLASSWKR, REGION, YEAR, ASECWT

---

## Project Structure

| Notebook | Description |
|---|---|
| `Racial_Wage_Disparities_Encoding.ipynb` | Decodes raw CPS numeric codes to readable labels for all variables |
| `Racial_Wage_Disparities_Dataset_Cleaning.ipynb` | Filters, cleans, and prepares the encoded dataset for analysis |
| `Racial_Wage_Disparities_EDA.ipynb` | Exploratory data analysis: visualizations of wage patterns by race and sector |
| `Racial_Wage_Disparities_ML.ipynb` | Mincer earnings regression estimating racial wage gaps by sector |

---

## Encoding

Raw CPS data uses numeric codes for all categorical variables. The encoding notebook maps these to readable labels for: SEX, RACE (consolidated to White, Black, Native, Asian, Other), HISPAN (with Hispanic ethnicity override on RACE), MARST, NATIVITY, REGION, UHRSWORKT, OCC1990, IND1990, CLASSWKR, and EDUC. Outputs `info.csv`.

---

## Data Cleaning

- Dropped non-informative flag columns
- Removed NIU (Not in Universe) values from UHRSWORKT
- Restricted to working-age adults (18–65)
- Filtered to active workers: INCWAGE > 0, WKSWORK1 > 0, UHRSWORKT > 0
- Dropped ambiguous education categories; mapped remaining EDUC values to years of schooling (EDUCYRS)
- Binary encoded HISPAN (1 = Hispanic, 0 = Not Hispanic)
- Binary encoded CLASSWKR (1 = government/public sector, 0 = private sector)
- Exported yearly panel observation counts
- Outputs `democleaned.csv`

---

## EDA

- Correlation heatmap across all numeric features
- Bar chart: average hourly wage by race/ethnicity, split by public vs private sector (CLASSWKR)
- Line plot: average hourly wage trends by race/ethnicity (2000–2023)
- Regional bar chart: average hourly wage comparison for Black vs Native workers across U.S. census divisions

---

## Regression

**Model:** Mincer earnings regression estimated via Weighted Least Squares (WLS)

**Dependent variable:** Log hourly wage (ln(INCWAGE / (WKSWORK1 × UHRSWORKT))), winsorized at 1st and 99th percentiles

**Independent variables:**
- Experience (AGE − EDUCYRS − 6) and experience squared
- Education fixed effects (C(EDUCYRS))
- Sex, marital status, nativity
- Race/ethnicity (reference: White)
- Sector (reference: private, CLASSWKR = 0)
- Race × Sector interaction term — isolates whether public sector employment narrows or widens racial wage gaps
- Region and year fixed effects
- Survey weights: ASECWT

---

## Results

**Model fit:** R² = 0.351, N = 1,402,419 observations

**Racial wage gaps in the private sector (relative to White workers):**
- Hispanic workers earn 14.1% less (coef = −0.141, p < 0.001)
- Black workers earn 11.8% less (coef = −0.118, p < 0.001)
- Native workers earn 11.3% less (coef = −0.113, p < 0.001)
- Other race workers earn 7.3% less (coef = −0.073, p < 0.001)
- Asian workers earn 0.8% more (coef = +0.008, p < 0.01)

**Public sector wage effect (race × sector interaction):**
- Black workers in the public sector earn 13.2 percentage points more relative to their private sector counterparts, nearly closing the racial wage gap (coef = +0.132, p < 0.001)
- Hispanic workers in the public sector earn 13.3 percentage points more relative to their private sector counterparts (coef = +0.133, p < 0.001)
- Native workers gain 6.3 percentage points in the public sector (coef = +0.063, p < 0.001)
- The public sector baseline coefficient is −0.087, meaning White workers earn slightly less in the public sector — the interaction terms show that minority workers benefit disproportionately from public employment

**Human capital returns:**
- Each additional education tier adds substantially to wages: a bachelor's degree yields a 72.7% wage premium over the Grade 9 baseline; a master's degree 92.3%; a professional degree 119.3%
- Experience returns are positive but concave: each additional year adds ~3.0% to wages with diminishing returns (EXPERIENCE_SQ = −0.0005)
- Male workers earn 23.4% more than female workers after controls

**Controls:**
- Foreign-born workers earn 11.9% less than the reference nativity group
- Pacific Division and New England are the highest-wage regions; East South Central the lowest
- Year fixed effects show consistent nominal wage growth across the panel

---

## Tools & Libraries

Python, pandas, numpy, matplotlib, seaborn, statsmodels
