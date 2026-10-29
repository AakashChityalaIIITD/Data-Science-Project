# Early Hospital Readmission of Diabetic Patients

**Course:** Data Science — Midterm Project  
**Notebook:** `pre_fin_final_with_processed_hypothesis_tests.ipynb`

This project explores the UCI *Diabetes 130-US Hospitals for Years 1999–2008* dataset. The target variable, `readmitted`, contains three classes:

- `NO`: the patient was not readmitted within 30 days / the label is no readmission.
- `>30`: readmission occurred after 30 days.
- `<30`: readmission occurred within 30 days.

The notebook contains the dataset checks, data preparation, before-and-after observations, EDA plots and the hypothesis-testing section.

## 1. Dataset

Source: UCI Machine Learning Repository, *Diabetes 130-US Hospitals for Years 1999–2008* (dataset ID 296).

Dataset page: <https://archive.ics.uci.edu/dataset/296/diabetes+130-us+hospitals+for+years+1999-2008>

The notebook looks for `diabetic_data.csv` in either of these locations:

```text
project-folder/
├── README.md
├── pre_fin_final_with_processed_hypothesis_tests.ipynb
└── diabetic_data.csv
```

It also checks this alternative path:

```text
project-folder/
├── pre_fin_final_with_processed_hypothesis_tests.ipynb
└── dataset_diabetes/
    └── diabetic_data.csv
```

If the file is not found, the notebook tries to download the UCI ZIP file automatically. If downloading fails, download the dataset from the UCI page, extract it, and put `diabetic_data.csv` in the same directory as the notebook.

## 2. Requirements

Use Python 3.10 or newer if possible. Install the packages used by the notebook:

```bash
python -m pip install --upgrade pip
python -m pip install numpy pandas matplotlib scipy scikit-learn jupyter
```

If your system uses `python3` instead of `python`, run the same commands with `python3 -m pip`.

## 3. How to run the notebook

1. Place the notebook and `diabetic_data.csv` in the locations described above.
2. Open a terminal and change directory to the project folder.
3. Start Jupyter:

   ```bash
   python -m jupyter notebook
   ```

   Alternatively, open the notebook in VS Code or upload it to Google Colab. If using Colab, upload `diabetic_data.csv` to the runtime or adjust the path in the dataset-loading cell.
4. Open `pre_fin_final_with_processed_hypothesis_tests.ipynb`.
5. Select **Restart Kernel and Run All Cells** (wording varies by application). Run from the first cell to the last cell; do not start directly at the hypothesis tests.
6. Wait for all cells to finish. The results are printed below the statistical-test cells, and the final cell prints a comparison table for the nine candidate tests.
7. Save the notebook after execution if you want to retain the newly generated outputs.

**Important:** A notebook file can contain empty output areas until its code cells have been run. Opening the file does not automatically calculate the results. The preprocessing cells must run successfully first because the inference section uses `df_clean` created earlier in the notebook.

## 4. Data preparation and analysis sample

The notebook performs data loading and checks the original dataset dimensions, then runs the implemented cleaning and preparation pipeline. The pipeline includes duplicate checks, invalid-value handling, exclusions for death/hospice discharge, retaining one encounter per patient, high-missingness and near-constant feature checks, missing-value treatment, numeric validity checks, PCA/Isolation Forest outlier filtering, and feature encoding.

The EDA uses the processed data before encoding. A bridge cell defines `df_eda` from `df_clean` and creates `early_readmit`, an indicator equal to 1 when `readmitted == '<30'` and 0 otherwise.

The hypothesis-testing section copies `df_clean` into `df_test`. It does **not** load the raw dataset again. This means the test variables retain the original readable categorical labels, such as `NO`, `>30`, `<30`, `Ch`, `No`, `Steady`, `Up`, and `Down`.

## 5. Hypothesis-testing section

The notebook includes nine candidate tests:

1. Poisson goodness-of-fit for the number of previous inpatient admissions.
2. Two-proportion z-test comparing prior inpatient admission in the `<30` and `NO` groups.
3. Chi-square test of independence between categorized previous inpatient admissions and readmission outcome.
4. Welch two-sample t-test comparing hospital-stay duration for `<30` versus `NO`.
5. Brown–Forsythe test comparing the spread of laboratory-procedure counts for `<30` versus `NO`, with a classical F-test as a sensitivity comparison.
6. Spearman rank-correlation test between hospital-stay duration and medication count.
7. Chi-square test between medication-change status and readmission outcome.
8. Chi-square test between insulin-treatment category and readmission outcome.
9. Two-proportion z-test comparing early-readmission proportions by diabetes-medication status.

For the tests, outputs may include the test statistic, degrees of freedom where applicable, p-value, effect estimate, 95% confidence interval, practical-size threshold, decision at significance level 0.05, and alternative-test or sensitivity comparisons. The last comparison table also reports Benjamini–Hochberg adjusted p-values across the nine candidate tests.

The assignment asks for **3–5 formal tests** in the final report. Select a justified subset and copy the results produced by running the notebook on the actual dataset. Do not assume that statistical significance by itself means the effect is practically important, and do not interpret observational associations as causation.

## 6. Troubleshooting

### `FileNotFoundError: diabetic_data.csv was not found locally and could not be downloaded`

- Check that the file is named exactly `diabetic_data.csv`.
- Put it beside the notebook, or inside `dataset_diabetes/`.
- Check the notebook's current working directory by running:

  ```python
  import os
  print(os.getcwd())
  print(os.listdir())
  ```

- If the file is elsewhere, move it to the expected location or update `LOCAL_CANDIDATES` in the dataset-loading cell.

### `NameError: df_clean is unavailable` or another variable is undefined

Restart the kernel and run all cells from the beginning. Variables are stored in the live Python kernel; they are not restored just because previous outputs are visible in the notebook.

### `ModuleNotFoundError`

Install the missing package using the same Python environment that runs Jupyter. For example:

```bash
python -m pip install scikit-learn
```

If Jupyter still cannot import the package, confirm which Python executable the notebook uses:

```python
import sys
print(sys.executable)
```

Then install the package through that executable's environment.

### A cell runs for a long time

The outlier-detection step and bootstrap confidence intervals can take time. Check whether the cell is still running. Run one cell at a time to identify which step is slow, but restart and run the notebook from the beginning before generating final results.

### No results appear under the hypothesis tests

Make sure the cells show as completed rather than still running, and that the cells in the statistical-inference section have actually executed. If a cell displays an error, resolve the first error in execution order and then run all cells again.

## 7. Reproducibility and reporting notes

- The notebook sets a fixed random seed (`20261019`) for its random procedures where implemented.
- Read and report the sample sizes printed by the current run; do not copy counts or p-values from an earlier run without checking that the code and dataset version match.
- State the actual preprocessing steps used by the code, including the PCA/Isolation Forest filtering step, when describing the EDA/test sample.
- Confidence intervals differ by test because they are constructed for different estimands and by different methods. Report what each interval estimates.
- This is an observational dataset. Findings describe associations in the processed sample and do not by themselves establish that a feature causes readmission.
