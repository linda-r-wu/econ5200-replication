# AI Appendix

## Sections 1 and 2

Sections 1 and 2 were completed without AI.

Name: Linda Wu
Date: 11/19/2026

Using AI in Sections 1 or 2 is an integrity violation. Do not edit the statement above; if it
is not true, talk to the instructor before you submit.

## How to file an entry

Sections 3 to 6 allow AI as a documented co-pilot under P.R.I.M.E. File one entry for each
piece of work you used it on, under the section it belongs to. Paste the full exchange: every
prompt as you sent it and every reply as it came back, including what you rejected. A summary
is not a transcript. If you cannot explain a line of what the model gave you, you cannot
submit it.

If you did not use AI in a section, write "No AI used in this section." under its heading.

Copy this block for each entry:

```
### Entry: TODO what it was for (for example, Exercise 1, not-yet-treated comparison)

- Model and version: TODO
- Date: TODO

**[Prep]** TODO: the role you gave the model.
**[Request]** TODO: what you asked for.
**[Iterate]** TODO: the constraints you added, and the version-2 prompt.
**[Mechanism Check]** TODO: what you asked it to explain, and whether the explanation held.
**[Evaluate]** TODO: how you checked the output against your own data or results.

**Full transcript**

TODO: paste the exchange here, unedited.

**What I changed, and why:** TODO, one line per change.
```

## Section 3: Robustness

### Exercise 1

### Entry: Why robust standard errors barely changed my results

- Model and version: ChatGPT GPT-6.0
- Date: 11/19/2026

**[Prep]** Act as a data scientist replicating Card’s 1995 paper, “Using Geographic Variation in College Proximity to Estimate the Return to Schooling.”
**[Request]** Explain why switching to robust standard errors changes the uncertainty around my estimate but leaves the schooling coefficient the same.
**[Iterate]** Use my coefficient of 0.1315, classical standard error of 0.0550 and robust standard error of 0.0541, and explain it in everyday language.
**[Mechanism Check]** Explain what robust standard errors allow to vary across people and whether they address weak instruments or problems with the exclusion restriction.
**[Evaluate]** Help me judge what the nearly unchanged standard error says about Card’s finding and where that reassurance ends.

**Full transcript**

input: Prep: Act as a data scientist replicating Card’s 1995 paper, “Using Geographic Variation in College Proximity to Estimate the Return to Schooling.”
Request: Explain why switching to robust standard errors changes the uncertainty around my estimate but leaves the schooling coefficient the same.
Iterate: Use my coefficient of 0.1315, classical standard error of 0.0550 and robust standard error of 0.0541, and explain it in everyday language.
Mechanism Check: Explain what robust standard errors allow to vary across people and whether they address weak instruments or problems with the exclusion restriction.
Evaluate: Help me judge what the nearly unchanged standard error says about Card’s finding and where that reassurance ends.

output: Switching to robust standard errors changes how you measure uncertainty around the estimate. The regression still uses the same data, controls and instrument, so the schooling coefficient stays at 0.1315, or 13.15 log points per additional year of schooling.

Classical standard errors assume that the unexplained part of wages has the same spread across people. Robust standard errors allow that spread to differ—for example, wages might vary more among people with more schooling, even after accounting for the controls.

Your standard error changes only slightly, from 0.0550 to 0.0541, so allowing for those differences barely affects how precise the estimate looks. Robust standard errors can increase or decrease, and here they make the interval slightly narrower, with the robust 95% interval running from 0.0253 to 0.2377.

That gives some reassurance that Card’s positive result is fairly stable to changing this assumption about wage variation. However, robust standard errors cannot strengthen a weak instrument or fix an invalid one. You still need to assess whether college proximity predicts schooling strongly enough and whether it affects wages through other channels, such as local job opportunities or family advantages. The nearly unchanged standard error supports the calculation of uncertainty under unequal error variances, while those causal questions remain open.

**What I changed, and why:** N/A

### Exercise 2

### Entry: Comparing schooling returns inside and outside metropolitan areas

- Model and version: ChatGPT GPT-6.0
- Date: 11/19/2026

**[Prep]** Act as a data scientist replicating Card’s 1995 paper, “Using Geographic Variation in College Proximity to Estimate the Return to Schooling.”
**[Request]** Help me write simple Python code that estimates the schooling return separately for men who grew up inside and outside metropolitan areas.
**[Iterate]** Use smsa66 to split the sample, remove it from the subgroup controls, and print each group’s sample size, schooling coefficient, robust standard error and robust first-stage F.
**[Mechanism Check]** Explain why a control that is constant within a group causes problems, and why positive estimates can still have intervals that include zero.
**[Evaluate]** Show me how to check that the groups contain 1,055 and 1,955 men, then help me interpret their estimates without assuming the difference between them is statistically significant.Show me how to check that the groups contain 1,055 and 1,955 men, then help me interpret their estimates without assuming the difference between them is statistically significant.

**Full transcript**

input: Prep: Act as a data scientist replicating Card’s 1995 paper, “Using Geographic Variation in College Proximity to Estimate the Return to Schooling.”
Request: Help me write simple Python code that estimates the schooling return separately for men who grew up inside and outside metropolitan areas.
Iterate: Use smsa66 to split the sample, remove it from the subgroup controls, and print each group’s sample size, schooling coefficient, robust standard error and robust first-stage F.
Mechanism Check: Explain why a control that is constant within a group causes problems, and why positive estimates can still have intervals that include zero.
Evaluate: Show me how to check that the groups contain 1,055 and 1,955 men, then help me interpret their estimates without assuming the difference between them is statistically significant.

output: Run this after loading `card` and defining `CONTROLS`, with `statsmodels.api` imported as `sm` and `IV2SLS` imported.

```python
area_controls = CONTROLS.copy()
area_controls.remove("smsa66")

for group in [0, 1]:
    sample = card[card["smsa66"] == group]

    X = sm.add_constant(sample[area_controls])

    area_fit = IV2SLS(
        sample["lwage"],
        X,
        sample["educ"],
        sample["nearc4"]
    ).fit(cov_type="robust", debiased=True)

    first_X = sm.add_constant(
        sample[["nearc4"] + area_controls]
    )

    first_fit = sm.OLS(
        sample["educ"], first_X
    ).fit(cov_type="HC1")

    print("Metropolitan area in 1966:", group)
    print("Sample size:", len(sample))
    print("Schooling coefficient:", round(area_fit.params["educ"], 4))
    print("Robust SE:", round(area_fit.std_errors["educ"], 4))
    print("Robust first-stage F:",
          round(first_fit.tvalues["nearc4"] ** 2, 1))
    print()
```

The filter selects men with `smsa66 = 0` for the outside group and `smsa66 = 1` for the inside group. We remove `smsa66` from their controls because everyone within each group has the same value. Among the inside group, it duplicates the intercept’s column of ones, while among the outside group it is entirely zeros, making the regression’s columns redundant.

Your results should round to:

| Childhood location | Sample size | Schooling coefficient | Robust SE | Robust first-stage F |
|---|---:|---:|---:|---:|
| Outside metropolitan areas | 1,055 | 0.1601 | 0.0714 | 8.2 |
| Inside metropolitan areas | 1,955 | 0.1066 | 0.0738 | 7.8 |

Check the sample counts with:

```python
outside_n = len(card[card["smsa66"] == 0])
inside_n = len(card[card["smsa66"] == 1])

print("Outside:", outside_n)
print("Inside:", inside_n)
print("Combined:", outside_n + inside_n)
print("Full sample:", len(card))

assert outside_n == 1055
assert inside_n == 1955
assert outside_n + inside_n == len(card)
```

Both estimates are positive, corresponding to **16.01 and 10.66 log points per additional year of schooling**, but a positive estimate can still have an interval that includes zero because the estimate comes with uncertainty. Your separately calculated Anderson–Rubin intervals are **[3.2, 44.5]** outside metropolitan areas and **[−6.3, 34.8]** inside them, in log points.

The inside-group interval leaves zero as a plausible value, while both first-stage F-statistics suggest caution about instrument strength. The larger outside-group estimate alone doesn’t establish a difference in returns between locations. A formal comparison would need to account for uncertainty in both estimates, and the overlapping intervals show why the point estimates alone give an incomplete picture.

**What I changed, and why:** N/A

### Exercise 3

### Entry: What happens if college proximity affects wages beyond schooling?

- Model and version: ChatGPT GPT-6.0
- Date: 11/19/2026

**[Prep]** Act as a data scientist replicating Card’s 1995 paper, “Using Geographic Variation in College Proximity to Estimate the Return to Schooling.”
**[Request]** Explain what my sensitivity exercise shows when I allow college proximity to affect wages through channels besides schooling.
**[Iterate]** Use the schooling coefficients of 0.1315, 0.1002, 0.0690 and 0.0065 under assumed direct wage effects of 0, 0.01, 0.02 and 0.04.
**[Mechanism Check]** Walk me through why subtracting the assumed direct wage effect from the reduced-form effect and dividing by the first-stage effect changes the schooling estimate.
**[Evaluate]** Explain what these scenarios imply about the exclusion restriction, while making clear that I have not estimated the actual direct effect or calculated confidence intervals for these scenarios.

**Full transcript**

input: Prep: Act as a data scientist replicating Card’s 1995 paper, “Using Geographic Variation in College Proximity to Estimate the Return to Schooling.”
Request: Explain what my sensitivity exercise shows when I allow college proximity to affect wages through channels besides schooling.
Iterate: Use the schooling coefficients of 0.1315, 0.1002, 0.0690 and 0.0065 under assumed direct wage effects of 0, 0.01, 0.02 and 0.04.
Mechanism Check: Walk me through why subtracting the assumed direct wage effect from the reduced-form effect and dividing by the first-stage effect changes the schooling estimate.
Evaluate: Explain what these scenarios imply about the exclusion restriction, while making clear that I have not estimated the actual direct effect or calculated confidence intervals for these scenarios.

output: Your sensitivity exercise asks **how much of the schooling return would remain if living near a college also helped wages through other channels**, such as local jobs or connections.

The baseline assumes that proximity’s entire wage effect comes through schooling. Your exercise gradually assigns some of that wage advantage to other channels, leaving less for schooling to explain.

| Assumed direct effect on log wages | Schooling coefficient | Return in log points per extra year |
|---|---:|---:|
| 0 | 0.1315 | 13.15 |
| 0.01 | 0.1002 | 10.02 |
| 0.02 | 0.0690 | 6.90 |
| 0.04 | 0.0065 | 0.65 |

The calculation follows this relationship:

\[
\text{Proximity’s wage effect}
=
\text{schooling return}\times\text{proximity’s schooling effect}
+\text{direct wage effect}.
\]

Rearranging gives:

\[
\text{Adjusted schooling return}
=
\frac{\text{proximity’s wage effect}-\text{assumed direct effect}}
{\text{proximity’s schooling effect}}.
\]

In your data, proximity predicts about **0.0421 higher log wages** and **0.3199 more years of schooling**. With no assumed direct effect, dividing the unrounded wage effect by the unrounded schooling effect produces **0.1315**. If you assume proximity directly adds **0.01** to log wages, you subtract that portion first, and the schooling return falls to **0.1002**.

This shows that **the size of Card’s estimate is sensitive to the exclusion restriction**. A direct benefit of one log point lowers the estimated return noticeably, while a four-log-point benefit leaves the schooling estimate close to zero. The positive point estimate survives the scenarios you tried, but its magnitude changes substantially.

These are hypothetical calculations. You haven’t estimated whether those direct benefits exist or whether their assumed sizes are realistic, and you haven’t calculated confidence intervals for the adjusted returns. The exercise shows what departures from the assumption could do to the estimate, while leaving open whether those departures actually occur.

**What I changed, and why:** N/A

### Exercise 4

### Entry: Checking family-background differences with father’s education

- Model and version: ChatGPT GPT-6.0
- Date: 11/19/2026

**[Prep]** Act as a data scientist replicating Card’s 1995 paper, “Using Geographic Variation in College Proximity to Estimate the Return to Schooling.”
**[Request]** Help me add simple code that prints a robust confidence interval and the number of observations lost in my father’s-education placebo regression.
**[Iterate]** Use my existing placebo_fit and placebo_sample objects, keeping the code basic and reporting the interval in years of father’s schooling.
**[Mechanism Check]** Explain why father’s education can reveal family-background differences and why a p-value of 0.0619 does not establish that the proximity groups are comparable.
**[Evaluate]** Help me interpret the coefficient of 0.3333, robust standard error of 0.1785 and loss of 690 observations, explaining what they suggest about Card’s assumption and what remains uncertain.

**Full transcript**

input: Prep: Act as a data scientist replicating Card’s 1995 paper, “Using Geographic Variation in College Proximity to Estimate the Return to Schooling.”
Request: Help me add simple code that prints a robust confidence interval and the number of observations lost in my father’s-education placebo regression.
Iterate: Use my existing placebo_fit and placebo_sample objects, keeping the code basic and reporting the interval in years of father’s schooling.
Mechanism Check: Explain why father’s education can reveal family-background differences and why a p-value of 0.0619 does not establish that the proximity groups are comparable.
Evaluate: Help me interpret the coefficient of 0.3333, robust standard error of 0.1785 and loss of 690 observations, explaining what they suggest about Card’s assumption and what remains uncertain.

output: Add this after your placebo regression. It uses the robust standard errors already stored in `placebo_fit`, assuming you fitted it with `cov_type="HC1"`.

```python
interval = placebo_fit.conf_int().loc["nearc4"]

print("Coefficient:", round(placebo_fit.params["nearc4"], 4))
print("Robust SE:", round(placebo_fit.bse["nearc4"], 4))
print("95% interval in years:", interval.round(4).tolist())
print("P-value:", round(placebo_fit.pvalues["nearc4"], 4))

print("Original sample:", len(card))
print("Placebo sample:", len(placebo_sample))
print("Rows dropped:", len(card) - len(placebo_sample))
```

Your coefficient of **0.3333** means that, after accounting for the controls, fathers of men who grew up near a college had about **one-third of a year more schooling**. Its robust standard error is **0.1785 years**, giving a 95% interval of approximately **[−0.017, 0.683] years**.

Father’s education helps check whether the proximity groups came from different backgrounds. More educated fathers might provide support, preparation or connections that also affect their sons’ wages, so a relationship between proximity and father’s education raises questions about how comparable the groups are.

The p-value of **0.0619** means the result falls just above the usual 5% cutoff. That gives insufficient evidence to reject a zero association at that cutoff, but the interval still allows a positive difference of roughly two-thirds of a year. There is enough uncertainty that you cannot confidently say the groups have similar family backgrounds.

You also lose **690 observations**, leaving **2,320 men**. If families with missing father’s education differ from those with recorded information, this smaller sample may give a different picture from the full sample. Overall, the check gives a reason to examine family-background differences more closely, while leaving uncertain whether those differences bias Card’s wage estimate and by how much.

**What I changed, and why:** N/A

## Section 4: Does the finding hold?

N/A

## Section 5: Peer audit

N/A

## Section 6: Your own repository

### Entry: TODO what it was for (for example, Exercise 1, not-yet-treated comparison)

- Model and version: ChatGPT GPT-6.0
- Date: 11/19/2026

**[Prep]** Act as a data scientist replicating Card’s 1995 paper, “Using Geographic Variation in College Proximity to Estimate the Return to Schooling.”
**[Request]** Help me test my GitHub repository on my Mac the way a third party or grader would, starting from a fresh download.
**[Iterate]** Give me Terminal commands one at a time to clone the repository into an empty folder, create a virtual environment, install the pinned packages from requirements.txt, and run python reproduce.py.
**[Mechanism Check]** Explain what each command does, why the fresh environment matters, and how the notebooks can run without depending on variables or files from my Colab session.
**[Evaluate]** Help me check that both notebooks finish, the data downloads automatically, and the headline table reproduces a schooling coefficient of 0.1315 with a standard error of 0.0550 when rounded. Show me how to measure runtime and troubleshoot any errors.

**Full transcript**

input: Prep: Act as a data scientist replicating Card’s 1995 paper, “Using Geographic Variation in College Proximity to Estimate the Return to Schooling.”
Request: Help me test my GitHub repository on my Mac the way a third party or grader would, starting from a fresh download.
Iterate: Give me Terminal commands one at a time to clone the repository into an empty folder, create a virtual environment, install the pinned packages from requirements.txt, and run python reproduce.py.
Mechanism Check: Explain what each command does, why the fresh environment matters, and how the notebooks can run without depending on variables or files from my Colab session.
Evaluate: Help me check that both notebooks finish, the data downloads automatically, and the headline table reproduces a schooling coefficient of 0.1315 with a standard error of 0.0550 when rounded. Show me how to measure runtime and troubleshoot any errors.

output: Save your latest changes to GitHub first, then open **Terminal** on your Mac and run these commands one at a time.

**1. Go to your Desktop.**

```bash
cd ~/Desktop
```

**2. Download a fresh copy into a new folder.**

```bash
git clone https://github.com/linda-r-wu/econ5200-replication.git card-grader-check
```

If `card-grader-check` already exists, choose another folder name.

**3. Go inside that folder.**

```bash
cd card-grader-check
```

**4. Check your Python version.**

```bash
python3 --version
```

Use a Python version supported by your pinned packages. Your previous successful test gives you a working version to use again.

**5. Create a fresh environment.**

```bash
python3 -m venv .venv
```

This creates a separate place to install the project’s packages, helping you check that the project works without relying on packages from your existing setup.

**6. Activate it.**

```bash
source .venv/bin/activate
```

You should see `(.venv)` at the beginning of your Terminal prompt.

**7. Install the pinned dependencies.**

```bash
python -m pip install -r requirements.txt
```

Wait until installation finishes and the prompt returns. If an error appears, fix that before continuing.

**8. Run the project and measure elapsed time.**

```bash
time python reproduce.py
```

Your runner should execute notebook 01 followed by notebook 02, each in a fresh kernel. That means each notebook must load its own data and define the variables and functions it needs.

Watch for the download message during this first run. If the data is already included in the fresh clone, the downloader may reuse it.

**9. Check the results.**

The run should finish without a traceback and print a populated headline table. Your headline values should round to:

| Result | Expected value |
|---|---:|
| Schooling coefficient, 2SLS | 0.1315 |
| Standard error, 2SLS | 0.0550 |

Values such as `0.131504` and `0.054964` match those rounded results.

You can check the output files with:

```bash
ls output/headline_table.csv output/executed/
```

You should see the CSV and executed copies of both notebooks. Check the elapsed time printed by `time` and record it in the README, stating whether the run included the data download and excluding package-installation time.

If the run fails, copy the final error and the cell shown above it. A `NameError` usually means a variable hasn’t been defined earlier in that notebook, while a missing-file error usually points to a path or download problem. After correcting the GitHub files, pull the changes with `git pull` and rerun, then repeat the fresh-clone test once the fixes are complete.

input: i got this error: NameError                                 Traceback (most recent call last)
Cell In[5], line 1
----> 1 headline = table.copy()

NameError: name 'table' is not defined

output: The error means notebook 01 reached `headline = table.copy()` before creating `table`.

Add your comparison-table code in a cell **above** that line, after the baseline code defining `CONTROLS`, `iv_fit` and `ols_fit`. The comparison code needs to create `rows` and then build the table:

```python
table = pd.DataFrame(
    rows,
    columns=[
        "estimate",
        "paper",
        "paper SE",
        "replication",
        "replication SE"
    ]
)

table["gap"] = table["replication"] - table["paper"]
table = table.set_index("estimate")
```

Save the updated notebook to GitHub, then run these commands in Terminal:

```bash
git pull
time python reproduce.py
```

This makes sure `table` exists before the next cell copies it and saves your headline results.

**What I changed, and why:** N/A
