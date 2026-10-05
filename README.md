# Pump Fault Detection

Fault detection for an industrial water pump using machine learning and deep learning.
Group project for PRT565 Machine Learning, Artificial Intelligence and Algorithms, Charles Darwin University (2026).

## The problem

When a pump fails without warning the plant stops and repairs are unplanned. This project tests whether a model can recognise an abnormal operating state from sensor readings alone, using 220,320 one-minute readings from 52 sensors on a single pump.

The task is supervised binary classification: NORMAL against ANOMALY (RECOVERING and BROKEN merged, because BROKEN occurs only 7 times).

## What makes this dataset hard

- **Imbalance.** 93.43% of rows are NORMAL. A model that always predicts NORMAL scores 93.43% accuracy and detects nothing, so models are judged on precision, recall, F1, ROC-AUC and PR-AUC.
- **The split trap.** Every anomaly falls between 12 April and 25 July. A standard last-20% test split lands entirely in August and contains zero anomalies. We split on a fixed date (1 July 2018) instead.
- **A sensor that cannot be trusted.** `sensor_50` is complete in the training period and missing for 86.2% of the test period. It was removed, along with `sensor_15` (100% missing).
- **Detection, not prediction.** RECOVERING always follows the failure, so 99.95% of the positive class is a pump that has already failed. The models detect a fault; they do not forecast one.

## Approach

| Stage | What we did |
|---|---|
| Cleaning | Dropped 2 sensors, linear interpolation of gaps, outlier clipping at the 0.1 and 99.9 percentiles |
| Features | 50 raw sensors plus 5-minute backward rolling mean and standard deviation: 150 features |
| Leakage control | Chronological split; clipping bounds, scaler and thresholds fitted on training or validation data only |
| Models | Decision Tree, Random Forest, Naive Bayes, ANN (MLP), LSTM |
| Tuning | GridSearchCV on the tree models; decision thresholds selected on a validation window |
| Diagnostics | Calibration, permutation importance, per-episode detection, K-means cross-check without labels |

## Results

Test period: 1 July to 31 August 2018 (89,280 minutes, 5,509 anomalous), default threshold 0.5.

| Model | Precision | Recall | F1 | ROC-AUC | PR-AUC |
|---|---|---|---|---|---|
| Decision Tree | 0.9779 | 0.9897 | 0.9838 | 0.9942 | 0.9722 |
| Random Forest | 0.9771 | 0.9893 | 0.9831 | 0.9999 | 0.9987 |
| LSTM | 0.9091 | 0.9927 | 0.9491 | 0.9993 | 0.9950 |
| ANN (MLP) | 0.8811 | 0.9938 | 0.9341 | 0.9992 | 0.9905 |
| Naive Bayes | 0.8725 | 0.9935 | 0.9290 | 0.9945 | 0.8776 |

- Accuracy is between 0.9906 and 0.9980 for every model and cannot rank them. PR-AUC can.
- Random Forest is the model we would deploy: best PR-AUC, best calibration, F1 within 0.001 of the best.
- GridSearchCV confirmed the hand-picked Random Forest and found a shallower Decision Tree with better PR-AUC but lower F1 at 0.5.
- Thresholds selected on the validation window lowered test F1 for every model, because that window is 22.9% anomalous and the test period is 6.2%.
- K-means with three clusters, given no labels, isolates the anomaly state with an F1 of 0.93.

These numbers come from the executed `notebook.ipynb` in this repository. The notebook also prints the tables itself (the cell above the conclusion).

## Repository layout

```
notebook.ipynb      final analysis with all outputs, from loading the data to the conclusion
old_notebook.ipynb  the notebook as submitted for Assessment 3, kept for reference
requirements.txt    Python dependencies (versions of the committed run)
DATA.md             where to get the dataset and where to put it
Figures/            fig_slide*: figures used in the presentation; fig1 to fig12: figures used in the report
Reports/            Assessment 3 slides and Assessment 4 report
```

## Running it

```
pip install -r requirements.txt
```

1. Download `sensor.csv` as described in [DATA.md](DATA.md) and place it next to the notebook.
2. Open `notebook.ipynb` and choose Restart and Run All.

A full run takes about 11 minutes on 2 CPU cores; the grid search is the slowest part. The neural models are seeded, but results can still differ slightly between machines and library versions. The report quotes the committed run, so do not commit a re-run without updating the report.

## Team

DRW Group 2, Darwin campus

| Member | Sections |
|---|---|
| Van Hoi (Hendrick) Dang | Problem, dataset, preprocessing and feature engineering |
| Avery Doan | Decision Tree, Random Forest, Naive Bayes, ANN, hyperparameter tuning |
| Jane Tran | LSTM, evaluation measures, results |

Assessment 3 (presentation) and Assessment 4 (report) both use this repository.

## Data source

Nphantawee. (2018). *Pump sensor data* [Data set]. Kaggle. https://www.kaggle.com/datasets/nphantawee/pump-sensor-data
