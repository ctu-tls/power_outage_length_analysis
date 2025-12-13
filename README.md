# Power Outage Length Analysis Based on Type of Cause
by Claire Tu (c7tu@ucsd.edu) and Po-Hua Chen (poc008@ucsd.edu)
[Website Link](https://ctu-tls.github.io/power_outage_length_analysis/))

## Introduction

### Power Outages

Power outages affect far more than just lights turning off. They interrupt daily routines, disrupt communication, pause business operations, and bring safety risks. Whether caused by extreme weather, equipment failures, or human activity, outages impact millions of households each year and expose how dependent our modern lives are on stable electricity. As climate events intensify and energy demand increases, understanding when and why outages occur has become increasingly important. In this project, we analyze major power outages in the United States to uncover trends over time, compare different causes, and identify factors that influence outage length. By approaching the data systematically, we aim to highlight why outage patterns could matter for communities, policymakers, and anyone who relies on a reliable power system.

### Dataset Description
In this project, our data came from a Purdue University dataset that contained power outage data in the continental U.S. from January 2000 to July 2016. There are 1534 rows x 56 columns in total. Each row in the dataset represents an outage event and contains information on when and where the outage occurred, the cause, duration, and the number of users affected.

We will use these data to analyze the following question: Does the length of power outage differ on natural reasons or human-caused reasons?

We will use the following columns in our analysis:

- `YEAR`: The year of the outage  
- `MONTH`: The month of the outage  
- `U.S._STATE`: The state of the outage  
- `NERC.REGION`: The geographical region assigned by the North American Electric Reliability Corporation (NERC)  
- `OUTAGE.START.DATE`: Date of the outage  
- `OUTAGE.START.TIME`: Time of the outage  
- `CLIMATE.REGION`: Climate region of the United States
- `OUTAGE.DURATION`: Duration of the outage in minutes
- `CAUSE.CATEGORY`: General cause of the outage

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning
We performed several cleaning steps to prepare the dataset for analysis:
1. Removed metadata rows and set the correct header row.
2. Dropped columns that were not relevant to our research question.
3. Converted numeric-like columns (e.g., outage duration, customers affected) to numeric dtype and handled non-numeric entries.
4. Focused on columns needed for EDA and modeling.

We handled missingness in two stages. First, we coerced numeric-like columns (e.g., OUTAGE.DURATION, CUSTOMERS.AFFECTED) to numeric using errors="coerce", so any non-numeric entries become NaN. Then, for each analysis/visualization, we used listwise deletion only on the variables needed: for example, the duration histogram drops rows missing OUTAGE.DURATION, and the scatter plot drops rows missing either OUTAGE.DURATION or CUSTOMERS.AFFECTED. For inference and modeling steps that require the cause label, we restricted to rows with non-missing CAUSE.CATEGORY (and non-missing target OUTAGE.DURATION when applicable), so results are based on comparable, well-defined subsets rather than mixing missing categories.

### Distribution of Cause Categories
This bar chart shows the frequency of each type of outage, with severe weather being the most frequent, followed by human-caused outages such as intentional attacks. Because weather-related events account for a large share of all outages, it is natural to compare the durations of natural and human-caused outages in later sections.
<iframe src="assets/distribution_cause_category.html" width="100%" height="520" style="border:none;"></iframe>

### Univariate Analysis (one-variable)
We first looked at the distribution of outage duration to understand typical outage lengths and whether there are heavy tails/outliers.

Interactive plot: Distribution of Outage Duration
[TODO: export Plotly HTML to assets/univariate_outage_duration.html and embed below]
<iframe src="assets/univariate_outage_duration.html" width="100%" height="520" style="border:none;"></iframe>
We also examined the frequency of different outage causes.

Static summary (cause frequency)
[TODO: short written summary of most common CAUSE.CATEGORY values.]

### Bivariate Analysis (two-variable)
Next, we explored whether outage duration changes with the scale of impact.

Interactive plot (Plotly): Customers Affected vs. Outage Duration
[TODO: export Plotly HTML to assets/bivariate_customers_vs_duration.html and embed below]
<iframe src="assets/bivariate_customers_vs_duration.html" width="100%" height="520" style="border:none;"></iframe>
[TODO: Add 1–2 sentences interpreting the relationship (trend? noisy? outliers?).]

### Aggregates (grouped / pivot table)

To connect outage duration with cause, we computed an aggregate table of median outage duration by CAUSE.CATEGORY.
[TODO: paste the markdown table generated from your pivot/groupby result]
[TODO: Insert df.to_markdown(index=False) output here]

[TODO: 2–3 sentence interpretation: which causes tend to have longer median durations, and why that might make sense.]

## Assessment of Missingness

### NMAR Discussion
We suspect `CUSTOMERS.AFFECTED` may be NMAR because small-scale outages that affect very few customers are less likely to be fully documented or may be omitted during data collection, making missingness more likely when the true number of affected customers is very small.

In the dataset, `CUSTOMERS.AFFECTED` is classified as NMAR (Not Missing At Random). When the number of people affected by a small outage is very small, it may be less likely to be fully documented or may be omitted. In other words, whether the data is missing may depend on the true but unobserved value of `CUSTOMERS.AFFECTED` itself, which is consistent with the definition and intuition of NMAR.

Observation of the data shows that `CUSTOMERS.AFFECTED` has a high missing rate, and the median of `OUTAGE.DURATION` is lower when the data is missing and higher when it is not, which is consistent with the idea that small-scale outages are more likely to be missed. However, NMAR cannot be statistically validated using observed data alone, and determining whether the missingness is truly NMAR would require the utility company’s internal reporting policies and omission thresholds.

### Missingness Dependency Tests (Permutation Tests)
We analyze the missingness of:

- **X** = `CAUSE.CATEGORY.DETAIL` (the column with non-trivial missingness)
- **Y** = `CAUSE.CATEGORY` (a column that X’s missingness *does* depend on)
- **Z** = `ANOMALY.LEVEL` (a column that X’s missingness *does not* depend on)

Test 1: Does missingness depend on ANOMALY.LEVEL (Z)?

- H0: Missingness of CAUSE.CATEGORY.DETAIL is independent of ANOMALY.LEVEL.
- H1: Missingness of CAUSE.CATEGORY.DETAIL depends on ANOMALY.LEVEL.
- Test stat: ∣mean (ANOMALY.LEVEL∣ 𝑋 missing ) − mean (ANOMALY.LEVEL∣ 𝑋 not missing )∣
- Observed stat: 0.0105
- p-value: 0.794
- α: 0.05 <br>
**Conclusion**: Fail to reject H0 → we do not find evidence that missingness depends on ANOMALY.LEVEL.

Test 2: Does missingness depend on CAUSE.CATEGORY (Y)?

- H0: Missingness of CAUSE.CATEGORY.DETAIL is independent of CAUSE.CATEGORY.
- H1: Missingness of CAUSE.CATEGORY.DETAIL depends on CAUSE.CATEGORY.
- Test statistic: TVD between the distribution of CAUSE.CATEGORY when CAUSE.CATEGORY.DETAIL is missing vs not missing
- Observed stat (TVD): 0.4107
- p-value: 0.0001
- α: 0.05 <br>
**Conclusion**: Reject H0 → missingness of CAUSE.CATEGORY.DETAIL does depend on CAUSE.CATEGORY (so it is not MCAR relative to this column).

Interactive plot (Plotly) related to missingness
<iframe src="assets/missingness_dep_on_cause_category.html" width="100%" height="520" style="border:none;"></iframe>

From the plot, the distribution of `CAUSE.CATEGORY` differs noticeably between rows where `CAUSE.CATEGORY.DETAIL` is missing vs not missing (some categories become much more/less common when the detail field is missing). This visual pattern is consistent with our permutation test result (large TVD and very small p-value), supporting the conclusion that the missingness of `CAUSE.CATEGORY.DETAIL` depends on `CAUSE.CATEGORY`.


## Hypothesis Testing
### Question
Do outages caused by **severe weather** tend to have different average duration than outages caused by **non-severe weather**?

### Hypotheses
- **H0:** The average outage duration is the same for severe weather outages and non-severe weather outages.
- **H1:** The average outage duration differs between severe weather outages and non-severe weather outages.

### Test Setup
- **Groups:**  
  - Group A = outages with `CAUSE.CATEGORY == "severe weather"`  
  - Group B = outages with `CAUSE.CATEGORY != "severe weather"`
- **Test statistic:** difference in mean outage duration  
  \[
  \Delta = \bar{D}_{A} - \bar{D}_{B}
  \]
- **Method:** permutation test (shuffle group labels)
- **Repetitions (reps):** **5000**
- **Significance level (α):** **0.05**

### Results
- **Observed difference in means (Δ):** **[PASTE_YOUR_OBSERVED_DIFF_HERE]**
- **p-value:** **0.0002**


### Conclusion

Because **p = 0.0002 < 0.05**, we **reject H0**. In other words, outage duration is **not** the same between severe-weather outages and non-severe-weather outages in our dataset; severe weather outages tend to have a meaningfully different average duration.

[TODO: IMPORTANT FIX]
Make sure your “natural vs human” mapping is clearly defined and defensible. If you only treat severe weather as natural, state that explicitly and consider re-framing as “severe weather vs non-severe weather” to avoid overclaiming.


### Framing a Prediction Problem

We frame a regression task to predict:
Response variable (y): OUTAGE.DURATION (minutes)
We evaluate performance using:
Metric: MAE (Mean Absolute Error), because it measures average absolute prediction error in minutes and is more interpretable than squared-error metrics.

Time of prediction
[TODO: Explicitly state what is known at prediction time and justify why CAUSE.CATEGORY and CUSTOMERS.AFFECTED are available (or revise features if they are not).]
### Baseline Model
##### Features

Quantitative: CUSTOMERS.AFFECTE
Categorical: CAUSE.CATEGORY (one-hot encoded)

##### Model
Pipeline with ColumnTransformer + OneHotEncoder + LinearRegression

##### Performance

Baseline MAE: [TODO: paste the exact number you got]

##### Interpretation

[TODO: 2–4 sentences—Is MAE “good”? What kinds of errors do you see? Any limitations of using only these two features?]


### Final Model

[TODO: Implement final model]
Requirements to satisfy:

Add at least 2 feature engineering steps (beyond just one-hot)
Perform hyperparameter search (e.g., GridSearchCV)
Keep everything in a single sklearn Pipeline
Compare on the same train/test split used in baseline
[TODO: Provide final MAE and explain why the added features should help.]

## Fairness Analysis

[TODO: Implement fairness analysis]
Requirements to satisfy:
Pick two groups (Group X / Group Y) based on a sensible attribute (e.g., CLIMATE.REGION, NERC.REGION, or state categories)
Compare model performance (e.g., MAE) across groups using the final model
Use a permutation test on the difference in metrics
Report H0/H1/test statistic/alpha/p-value/conclusion
