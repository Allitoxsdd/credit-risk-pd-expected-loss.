1 – Data Understanding & Cleaning

Prepare a clean, well-understood version of the UCI "Default of Credit Card Clients" dataset to be used for credit risk modeling (Probability of Default and Expected Loss).



- Source: UCI Machine Learning Repository – *Default of Credit Card Clients*.  
- Population: Taiwanese credit card customers.  
- Target variable: `default.payment.next.month`  
  - `1` = customer defaulted on the next month’s payment  
  - `0` = customer did not default  
- In this project we treat this as a **1-month default indicator** and use it as our binary PD target.


- Loaded the original Excel file using `pandas.read_excel`.
- Dropped the `ID` column (pure identifier, not informative for modeling).
- Verified:
  - Number of rows and columns.
  - Presence and distribution of the target variable.
  - Basic data types for each column.


Columns were grouped into:

- **Categorical variables**  
  `SEX`, `EDUCATION`, `MARRIAGE`, `PAY_0`, `PAY_2`, `PAY_3`, `PAY_4`, `PAY_5`, `PAY_6`, `default.payment.next.month`

- **Numeric variables**  
  `LIMIT_BAL`, `AGE`, all `BILL_AMT*` (bill statements) and `PAY_AMT*` (payments).

Data types were standardized:

- Categorical variables cast to integer (and treated as categories for analysis).
- Numeric variables cast to `float64`.


- Checked for **missing values** across all columns.  
  The original UCI dataset does not contain explicit NaNs, but we still verified this in case of future transformations.
- Identified **inconsistent or undocumented category codes**:
  - `EDUCATION` contained values {0, 5, 6}, which are not part of the official coding scheme.
  - `MARRIAGE` contained some `0` values, also undocumented.
- Applied the following cleaning rules:
  - Mapped `EDUCATION` values {0, 5, 6} → `4` ("Others/Unknown").
  - Mapped `MARRIAGE` value `0` → `3` ("Others").



- Generated summary statistics for numeric features:
  - `LIMIT_BAL`, `AGE`, `BILL_AMT*`, `PAY_AMT*`
  - Looked at mean, std, and selected percentiles (1%, 5%, 50%, 95%, 99%) to detect extreme values and skewness.
- Created frequency tables for key categorical variables:
  - `SEX`, `EDUCATION`, `MARRIAGE`
- Verified that the **overall default rate** is reasonable and consistent with published information for this dataset.


- **Default rate by payment status** (`PAY_0`):  
  Confirmed that more severe recent payment delays are associated with higher default rates.
- **Default rate by credit limit buckets** (`LIMIT_BAL` quintiles):  
  Checked whether customers with lower or higher credit limits exhibit different default behavior.

2 – Exploratory Data Analysis (EDA)

 
Develop a credit-risk-focused understanding of the dataset by exploring how key features relate to the probability of default, identifying important predictors, and spotting potential data issues before modeling.

Univariate Distributions

- Examined the distributions of key numeric variables:
  - `LIMIT_BAL` (credit limit), `AGE`
  - Bill statement amounts (`BILL_AMT1`–`BILL_AMT6`)
  - Payment amounts (`PAY_AMT1`–`PAY_AMT6`)
- Observed that:
  - Monetary variables are highly skewed, with a large mass near zero and a long right tail.
  - Credit limits and bill amounts vary over several orders of magnitude, consistent with heterogeneous customer segments.

Categorical Feature Exploration

- Analyzed the frequency and distribution of:
  - `SEX`, `EDUCATION`, `MARRIAGE`
  - Payment status variables `PAY_0`, `PAY_2`, `PAY_3`, `PAY_4`, `PAY_5`, `PAY_6`
- Verified that category levels and frequencies are consistent with expectations after the cleaning in Stage 1.

Default Rate by Feature 

- Computed and visualized **default rates** across:
  - Categorical variables: `SEX`, `EDUCATION`, `MARRIAGE`, `PAY_*`
  - Bucketed numeric variables:
    - `LIMIT_BAL` (credit limit quintiles)
    - `AGE` bands (e.g., 20–29, 30–39, etc.)
- Key observations (to be refined as modeling progresses):
  - Higher recent payment delay codes (`PAY_0`, etc.) are associated with significantly higher default rates.
  - Default behavior varies across credit limit buckets and age groups, suggesting segmentation effects.

Correlation & Redundancy

- Computed a correlation matrix for numeric features (especially `BILL_AMT*` and `PAY_AMT*`).
- Identified strong correlations among consecutive bill/payment amounts, which:
- Later modeling may need to handle redundancy (e.g., via feature selection, regularization, or engineered summaries).

The EDA results guide the design of the **Probability of Default (PD) models** 
