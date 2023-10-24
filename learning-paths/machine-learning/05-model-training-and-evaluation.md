# Model training and evaluation

Before training your model, you need to format the data as per [[04-data-preprocessing]], then you are able to train the model and evaluate the results from the different models created.

There are some preliminary steps that you have to include when converting the dataset from a DataFrame to a format that a machine learning algorithm can use. For Amazon SageMaker, here are the steps you need to take:

- Split the data into `train_data`, `validation_data`, and `test_data` using `sklearn.model_selection.train_test_split`.
- Convert the dataset to an appropriate file format that the Amazon SageMaker training job can use. This can be either a CSV file or record protobuf. ([see Common Data Formats for Training](https://docs.aws.amazon.com/sagemaker/latest/dg/cdf-training.html)).
- Upload the data to your Amazon S3 bucket.

If the data provided is not in a `.csv` e.g. is in a `*.data` format, will need to write the data into a DataFrame for easier visualisation and analysis. Also, adding the attribute names, which are from the file in the dataset package.

```python
df = pd.DataFrame(data=np.array(data).astype(float), columns=["column_1",
            "column_2",
            "column_3"])
```

### Training the model
#### Train-test-validation split

It is necessary to split the dataset into training, validation and test subsets. The sklearn package can help accomplish this, using [`train_test_split()`](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html).  Use this function **twice** to split the data into **training (80%)**, **validation (10%)**, and **test (10%)** subsets.

```python
# Get the feature values until the target column (not included)
X = df.values[:, :-1].astype(np.float32)

# Get the target column
y = df.values[:, -1].astype(np.float32)

# Get 80% of the data for training; the remaining 20% will be for validation and test
# This function splits input feature `X` and target `y` pairs by a given ratio (test_size). This specific example splits the data into training (80%) and test (20%) subsets.
train_features, test_features, train_labels, test_labels = train_test_split(X, y, test_size=0.2)

# Split the remaining 20% of data as 10% test and 10% validation
test_features, val_features, test_labels, val_labels = train_test_split(test_features, test_labels, test_size=0.5)
```

Use pandas [`shape`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.shape.html) to get the number of columns and rows for each.

```python
# Use the function to create your datasets
train_features, test_features, train_labels, test_labels, val_features, val_labels = create_training_sets(df)

..
print(f"Length of train_features is: {train_features.shape}")
print(f"Length of train_labels is: {train_labels.shape}")
print(f"Length of val_features is: {val_features.shape}")
print(f"Length of val_labels is: {val_labels.shape}")
print(f"Length of test_features is: {test_features.shape}")
print(f"Length of test_labels is: {test_labels.shape}")
```

After splitting the dataset into subsets, use Amazon SageMaker [`LinearLearner()`](https://sagemaker.readthedocs.io/en/stable/algorithms/tabular/linear_learner.html) , which will create a training job within your AWS account, you will need to ensure you have the correct roles to execute the job.

```python
# Call the LinearLearner estimator object
# Instantiate the LinearLearner estimator 'regressor' object with one ml.m4.xlarge instance
regression_model = sagemaker.LinearLearner(role=sagemaker.get_execution_role(),
                                               instance_count=1,
                                               instance_type='ml.m4.xlarge',
                                               predictor_type='regressor')
```

> [!IMPORTANT]
> 
>In a production pipeline, it is recommended to convert the data to the Amazon SageMaker protobuf format and store it in Amazon S3. However, to get up and running quickly, AWS
>provides the convenient method `record_set` for converting and uploading when the dataset is small enough to fit in local memory. It accepts NumPy arrays like the ones you already have, so let's use it here. The `RecordSet` object will keep track of the temporary Amazon S3
>location of your data. Use the `estimator.record_set` function to create train, validation, and test records. Then, use the `estimator.fit` function to start your training job.

Next use [`record_set()`](https://sagemaker.readthedocs.io/en/stable/algorithms/tabular/linear_learner.html#sagemaker.LinearLearner.record_set) function from [`LinearLearner`](https://sagemaker.readthedocs.io/en/stable/algorithms/tabular/linear_learner.html)(`regression_model`) to set the training, validation, and test parts of the estimator.

```python
train_records = regression_model.record_set(train_features, train_labels, channel='train')
val_records = regression_model.record_set(val_features, val_labels, channel='validation')
test_records = regression_model.record_set(test_features, test_labels, channel='test')
```

The [`fit()`](https://sagemaker.readthedocs.io/en/stable/algorithms/tabular/linear_learner.html#sagemaker.LinearLearner.fit) function applies regression on this data. This will start a training job within SageMaker with the configuration specified earlier.

```python
regression_model.fit([train_records, val_records, test_records])
```

> [!NOTE]
> 
> When using SageMaker `fit()`, the following happens in the background:
> 1. Provisions instances
> 2. Loads algorithm container
> 3. Loads data from S3
> 4. Trains model
> 5. Outputs model artifacts to S3
> 6. Tears down training cluster

Alternatively you can pass an in-memory array, you can pass in an S3 path as thats were SageMaker training can picking up the data.

```python
regression_model.fit({'train': sagemaker.s3_inputs(inputs, distribution='FullyReplicated', content_type='text/plain')})
```

### Evaluating the model
#### Amazon SageMaker analytics

You can use [`sagemaker.analytics`](https://sagemaker.readthedocs.io/en/stable/api/training/analytics.html) to get some performance metrics. This doesn't require deploying the model. Because this is a regression problem, you can check the **mean squared error**.

```python
sagemaker.analytics.TrainingJobAnalytics(regression_model._current_job_name, 
                                         metric_names = ['test:mse']
                                        ).dataframe()
```

#### Deploying the model

Deploying your model, involves creating an [endpoint](https://docs.aws.amazon.com/sagemaker/latest/APIReference/API_runtime_InvokeEndpoint.html) , that is connected to the model created from the earlier training job. And is linked to the model and training job used, additionally the model output is stored within an S3 bucket (model.tar.gz).

```python
regression_predictor = regression_model.deploy(initial_instance_count=1, instance_type='ml.m4.xlarge')
```

> [!IMPORTANT]
> 
> There is a soft limit within AWS and will require you to request an increase at account level. For example `ml.m4.xlarge` on my account had a quota of 0 and required me to request an increase on the [Service Quotes](https://eu-west-2.console.aws.amazon.com/servicequotas/home/services/sagemaker/quotas). This can take a couple of days to be processed, recommended to use a smaller tier.

As an endpoint is now ready for use, we can use [`predict()`](https://sagemaker.readthedocs.io/en/stable/api/inference/predictors.html#sagemaker.predictor.Predictor.predict).

```python
regression_predictor.predict(batch)
```

It is necessary to calculate the mean squared error

mean square error = $\displaystyle\frac{1}{n}\sum_{t=1}^{n}(pred_t-target_t)^2$

```python
mse = sum((preds - labels)**2) / len(preds)
```

#### Plotting results

Even though you calculated the mean squared error, it is still a good idea to examine the results visually.

##### Scatter Plot

With the scatter plot, you look at the fitted (predicted) values vs. true values. From the following plot, you can see that the predicted and true values followed a similar pattern overall.

##### Residual plot

Every data point has a residual. It is the difference between the real value and the predicted value. Residuals are calculated like this:  

$e = y - \hat y$  

A positive residual means a higher prediction than true value, and a negative residual means a lower prediction than true value. If there is a random pattern in residuals, this is good. Patterns you might see are the U-shape or inverted U-shape patterns. These two patterns point out some non-linearity in the data, which needs to be fixed.

In short 
- Random: This means the data is linear. [^1]
- U or inverse U shape: This points out some non-linearity in the data.

> [!NOTE]
> 
> Don't forget to clean up the endpoint after checking the predictions. Using `delete_endpoint()` on the deployed endpoint within SageMaker.

### Summary

In summary, developed a regression model using the linear learning algorithm in Amazon SageMaker. Also covering important concepts, such as training, validation, and test splits; the mean squared error regression metric; and analysis of residual error.

[^1]: random residuals, mean the data is linear.

---

#aws #matplotlib #pandas  #sklearn #sagemaker #model-training #model-evaluation 