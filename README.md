# Power-network disruption: does local position help predict later interruptions?

Final project, Complex Systems and Applications, Option 1.

**Question:** One substation has already lost power. For the substations still working, does their position in the physical network help predict a simulated interruption in the next 24 hours, beyond load ratio and equipment age?

## Files

| File | What it is |
|---|---|
| `power_network_disruption.ipynb` | The completed notebook, with every cell run and a portfolio summary at the top |
| `power_network_disruption.html` | The same notebook as a web page, for reading without Jupyter |
| `power_substations.csv`, `power_links.csv` | The synthetic course data, so the notebook also runs offline |

## Data

- **144 substations** with load ratio, equipment age, whether they were interrupted at the start, and whether they were interrupted later
- **344 physical links** between substations
- One substation (S066) is interrupted at the start. That leaves **143 eligible substations**, and 39 of them (27%) are interrupted later.

## Method

1. **Exploration.** Substations interrupted later carry a higher load (median 0.74 vs 0.59) and are older (24 vs 18 years). The two groups overlap a lot, so these two attributes alone cannot separate them.
2. **Network.** I built an undirected graph with `networkx` and calculated two features for each substation:
   - **degree**: the number of direct links
   - **local clustering coefficient**: how many of a substation's neighbours are also linked to each other

   The two features are negatively correlated in this network.
3. **Models.** I compared two logistic regressions (`StandardScaler` + `LogisticRegression`) on one stratified 70/30 split, with scaling fitted on training rows only:
   - **A:** load ratio + equipment age
   - **B:** A + degree + local clustering
4. **Robustness check (beyond the brief).** I repeated the comparison on 200 different stratified splits.

## Results

On one split, with 43 test substations and 12 later interruptions:

| | A: attributes only | B: with network features |
|---|---|---|
| Accuracy | 0.767 | 0.860 |
| F1 (later interruption) | 0.444 | 0.750 |
| ROC AUC | 0.828 | 0.949 |
| Missed interruptions | 8 of 12 | 3 of 12 |
| False alarms | 2 | 3 |

Averaged over 200 splits:

| | A | B |
|---|---|---|
| Mean F1 | 0.316 | 0.466 |
| Mean ROC AUC | 0.744 | 0.786 |

Model B has the higher ROC AUC in 84% of the splits and the higher F1 in 86%.

## Interpretation

Network position adds predictive information, and the result holds across splits. The gain on the single split is larger than usual, though. The 200-split average shows a smaller gain that is more realistic.

## Limitations

- The data are synthetic and the test set is small.
- No cascade is simulated. The results show association, not that interruptions spread through the network or cause each other, and the model is not suitable for operating a real grid.

## Ethics

A false alarm sends crews to equipment that is working and delays repairs elsewhere. A missed interruption leaves a community without power. Engineers and the grid operator should decide how to weigh the two errors.

## Tools

Python · pandas · networkx · scikit-learn · matplotlib
