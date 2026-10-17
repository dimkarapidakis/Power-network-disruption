# Hospital infection control: can earlier transfers help predict later clusters?

Final project, Complex Systems and Applications, Option 1.

For wards without an active infection cluster, do patient transfers recorded in the previous 14 days help predict a new simulated cluster in the following 14 days, beyond occupancy and device use?

## Files

| File | What it is |
|---|---|
| `Option1_Hospital_Infection_Control_SOLUTION_Claude.ipynb` | The completed notebook, all cells run, portfolio summary at the top |
| `Option1_Hospital_Infection_Control_SOLUTION_Claude.html` | The same notebook as a web page |
| `hospital_wards.csv`, `hospital_transfers.csv` | The synthetic course data, so the notebook runs offline |

## Method

A directed, weighted transfer network of 120 wards and 420 links. Two network features per ward: total incoming transfer volume, and incoming volume from wards already affected. Logistic regression on occupancy ratio and device-use share, compared with the same model plus the two network features, on one stratified 70/30 split of the 102 eligible wards, with scaling fitted on training rows only.

## Results (31 test wards)

| | Attributes only | With network features |
|---|---|---|
| F1 (later cluster) | 0.435 | 0.737 |
| ROC AUC | 0.693 | 0.904 |

The network features removed all 6 false alarms; 5 of the 12 later clusters were still missed. The data are synthetic and the test set is small: this shows an association, not that transfers cause clusters.

## Running

Open the notebook in Jupyter or Google Colab and run all cells. Requires `pandas`, `numpy`, `networkx`, `scikit-learn`, `matplotlib`.
