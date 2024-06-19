# DP 100 Data Scientist Notes

Below are a series of notes taken for the different aspects of understanding using Azure Machine Learning services that will be used by a Data Scientist.

## Regression models

These notes are related to selecting the right algorithm to create a model in Azure Machine Learning designer:

- The Multiclass Decision Forest algorithm will make a prediction between several categories.
- The Bayesian Linear Regression algorithm will forecast a numeric value, not make a prediction between several categories.
- The Boosted Decision Tree Regression algorithm will forecast a numeric value, not make a prediction between several categories.
- The K-means algorithm will separate similar data points into groups, not make a prediction between several categories.

## Automated ML experiments

These notes are related to selecting appropriate evaluation metrics for automated machine learning:

- The precision metric will result in a model that minimizes false positive rate.
- The recall metric will result in a model that minimizes false negative rate. The accuracy metric will result in a model with the highest accuracy.
- The AUC metric will result in a model with the highest area under receiver operating characteristic curve.

## Azure ML Designer

These notes are related to using custom code components in designer:

- An Execute Python Script component must implement an `azureml_main` method.
- A Create Python Model component must implement a train and predict methods.
- The `load_data` method is used to construct a data object but is not part of the Execute Python Script component.

## Machine Learning Algorithms

These notes are related to algorithms in Azure Machine Learning designer:

- The Two Class Logistic Regression, Two Class Support Vector Machine, and Linear Regression all rely on a linearity assumption in the problem they are used to solve.
- Only the Two Class Boosted Decision Tree allows for non-linearity.

## Azure AutoML Computer Vision

These notes are related to computer vision tasks supported by Azure AutoML via the Azure Machine Learning python SDK v2:

- `Image_classification_multilabel` is used for predicting multiple tags for a given image.
- `Image_classification` is for predicting a single tag for a given image.
- `Image_instance_segmentation` is for making a prediction for each pixel in the input image.
- Only `image_object_detection` is used to predict bounding box coordinates around objects in a given image.

## Evaluate Models

These notes are related to metrics for use during notebook training:

- Recall, Precision, and Accuracy are classification metrics unsuitable for time series forecasting.
- Root mean squared error is an appropriate metric for time series forecasting.

## Hyperparameters

These notes are related to selecting an appropriate sampling method for hyperparameter tuning based on the type of hyperparameters:

- To cover all the combinations of the hyperparameters, grid search is the only sampling technique that works, and it supports discrete hyperparameters only.

## Feature Importance

These notes are related to generating feature importance data:

- The local feature importance will explain what influences the result for a single prediction.
- Application Insights will allow to collect telemetry from the model but will not allow to explain the influencers for a single prediction.
- Analyzing the recall for a cohort will allow to identify unfairness in the mode, but not to explain the influencers for a single prediction.
- The global feature importance will explain the model's predictions across the whole dataset but not a single prediction.

## Model Fairness

These notes are related to evaluating model fairness based on prediction disparity:

- Analyzing the recall of different cohorts will tell the prediction performance of the model and its fairness.
- Analyzing the selection rate will not tell the prediction performance of the model and its fairness.
- The global influencers will quantify the relative importance of each feature but not detect unfairness in the model.
- Application Insights is useful to collect telemetry and monitor the model, but it will not ensure the model is fair.

## Azure VM sizes

These notes are related to configuring compute for a job run:

- From the options available, the NVv3 series is the only one featuring GPUs.
- The DSv3 instances are general purpose compute.
- The ESv3 instances are memory optimized compute. Finally, the HBv3 instances are high performance compute.

---
#azure #ml #mlops #automl
