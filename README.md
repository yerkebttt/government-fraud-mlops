# Government Fraud Detection MLOps Project

A Databricks prototype that scores fictional government-support applications for possible fraud. It demonstrates a machine-learning workflow from training and model serving to monitoring and retraining.

> **Important:** The application records and fraud labels are synthetic. This project demonstrates a technical workflow; it does not establish that the model is suitable for real fraud decisions.

## Project workflow

1. **Train and deploy** — prepare synthetic data, train a logistic regression model, register it with MLflow, and serve it through a Databricks REST endpoint.
2. **Simulate and test** — send twelve monthly batches of fictional applications to the endpoint.
3. **Monitor** — check prediction coverage, available fraud labels, and changes in incoming data.
4. **Retrain and compare** — train a candidate model and compare it with the currently served version before making a promotion decision.

## Repository contents

| Notebook | Purpose |
|---|---|
| `01_train_deploy.ipynb` | Creates the baseline data, trains and evaluates the model, registers it, and deploys it. |
| `02_monitor.ipynb` | Summarizes predictions, available labels, and data drift. |
| `03_simulate_and_test.ipynb` | Creates monthly test batches and sends them to the serving endpoint. |
| `04_retrain.ipynb` | Trains a candidate model and compares it with the serving model. |

## Model and baseline results

The baseline contains 5,000 fictional applications, with a simulated fraud rate of 6.62%. The model uses logistic regression and six application features. Records are split chronologically into training, validation, and test sets.

On the held-out test set of 750 applications:

- ROC-AUC: **0.742**
- Precision at the selected threshold: **21.6%**
- Recall at the selected threshold: **53.3%**
- Applications flagged for review: **14.8%**

The threshold was selected to keep the review volume near a 15% limit. These results apply only to the synthetic dataset.

## Serving, simulation, and monitoring

The Databricks Model Serving endpoint returns a fraud probability and a review flag. The simulation sends 12 monthly batches of 250 applications each, for 3,000 predictions.

Monitoring found a change in the simulated input data from July through December. A drift flag indicates that the data pattern changed; by itself, it does not prove that model accuracy decreased. Confirmed fraud labels are needed to measure performance.

## Retraining decision

The retraining notebook registered a candidate model as version 4 and compared it with serving version 3. At a 0.50 threshold, version 4 flagged substantially more applications for review. Version 3 therefore remained live in the project.

This demonstrates why model promotion should consider both prediction metrics and the review team's capacity.

## Running the notebooks

Run these notebooks in a Databricks workspace configured for the project's Unity Catalog, MLflow model registry, and Model Serving endpoint:

1. `01_train_deploy.ipynb`
2. `03_simulate_and_test.ipynb`
3. `02_monitor.ipynb`
4. `04_retrain.ipynb`

The notebooks may create or update tables, registered models, and a serving endpoint. Use a Databricks workspace where you have permission to perform those actions. Notebook outputs are not included in this repository, so run the notebooks in Databricks to reproduce the results.

## Limitations

- Data and labels are fictional, not real government application data.
- Synthetic results do not establish performance on real cases.
- Drift is a signal to investigate, not proof of model failure.
- A real deployment would require approved representative data, access controls, agreed review thresholds, operational alerts, and human oversight.

## Reproducibility

The implementation is provided as Databricks notebooks in this repository. To reproduce the workflow, open the notebooks in a suitably configured Databricks workspace and run them in the order listed above.
