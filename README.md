# Airplane Crash Fatalities Prediction (Regression)

**Author:** Maria | **Date:** 19/06/2026 | **Version:** v1.0   | **Github:** [Airplane Regression Project](https://github.com/vcmeamaria/airplane_regression_project)

## Overview

This notebook predicts the number of fatalities in an airplane crash. The target variable is `Fatalities`, which is a count, so this is a regression problem.

The training data is `Airplane_Crashes_and_Fatalities_Since_1908_data.csv`, which has 4,741 records and 13 columns. The columns include the date, location, operator, aircraft type, number of people on board, ground casualties and a free text summary. The files were read with latin1 encoding because they contain special characters.

Performance was judged with R squared, RMSE, MSE and MAE.

## Unseen data

The final model is applied to `Airplane_Crashes_and_Fatalities_Since_1908_unseen.csv`. This file has 527 records and 12 columns. It has the same features as the training data but does not include the `Fatalities` column, since that is what the model predicts.

## Preprocessing decisions

This dataset needed a lot of cleaning. Several columns had many missing values, with Flight number, Time, Route and the construction number being the worst. Eleven rows were missing the target value and were removed, since the target is needed for training. There were no duplicate rows.

The target was very right umbalanced, with most crashes causing few deaths and a small number causing very many. A 'log1p' transformation was applied, which made the distribution much more balanced and reduced the influence of extreme values. Predictions are converted back to the original scale before scoring.

Columns that were sparse, were identifiers, or were free text were dropped: Time, Flight number, Route, construction number, Registration and Summary. New features were then engineered from the remaining columns. Year, Month and Decade were taken from the date. A binary military flag was built from the operator name. A country feature was taken from the last part of the location string. A manufacturer feature was taken from the first word of the aircraft type. The original date, operator, location and type columns were then removed.

Country and manufacturer had hundreds of unique values, so they were frequency encoded rather than one hot encoded, which keeps the dataset compact. Any values not seen in training were mapped to zero. Remaining gaps in the numeric features were filled with the training median, with the medians learned from the training data only to avoid leakage. The large 9/11 ground casualty value was kept, since it is a real event. Finally the eight features were standardised with StandardScaler, fitted on the training portion only.

The data was split 80 to 20 with a fixed random state, giving 3,784 training rows and 946 test rows.

## Models trained and the final choice

Four models were trained and tested: linear regression as a baseline, a decision tree, a random forest and gradient boosting. The decision tree depth was chosen by cross validation.

Gradient boosting was chosen as the final model. It gave the best R squared and the lowest errors of the four. Linear regression performed poorly, with a negative R squared, which suggested the relationships in the data were not linear. Feature importance from the tree based models showed that the number of people on board was by far the strongest predictor.

## How the model was tuned

Gradient boosting was tuned with GridSearchCV using 5 fold cross validation, optimised on R squared. The grid covered the number of estimators, the maximum depth and the learning rate. The best settings were a learning rate of 0.05, a maximum depth of 3 and 100 estimators.

## Final performance on the test set

The tuned gradient boosting model scored as follows on the test set:

- R squared 0.394
- RMSE 23.325
- MSE 544.047
- MAE 9.395

This was a small improvement over the untuned gradient boosting model, which had an R squared of 0.379 and an RMSE of 23.605.

## Predictions file

The predictions were saved to `airplane_fatalities_scenario_2.csv`. It contains a single column of predicted fatalities, one row for each of the 527 unseen records. The values were converted back from the log scale and clipped so none are negative. The predicted counts ranged from about 1 to about 66, with most crashes predicted to have lower fatality counts, which follows the pattern seen in the training data.

## Limitations and future work

Some useful information could not be used, in particular the free text summaries. The data also only runs up to 2009, so it may not reflect modern aviation. Future work noted in the notebook includes applying natural language processing to the text fields, adding more recent accident data, testing further models, and trying other feature engineering approaches.

---

AI disclosure: AI was utilised to support coding fixing & markdown improvement.
