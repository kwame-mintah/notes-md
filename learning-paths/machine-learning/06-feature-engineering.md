# Feature Engineering

Feature engineering is the act of making data easier for a machine learning model to understand. It's not adding anything new but reshaping and curating the existing data to make the existing patterns more apparent. Feature engineering is the process of using domain knowledge of the data to create features that make machine learning algorithms work better than they would on a simple raw encoding.

## Feature engineering with SageMaker Processing

Amazon SageMaker Processing allows you to run steps for data pre- or post-processing, feature engineering, data validation, or model evaluation workloads on Amazon SageMaker. Processing jobs accept data from Amazon S3 as input and store data into Amazon S3 as output.

![](https://sagemaker.readthedocs.io/en/stable/_images/amazon_sagemaker_processing_image1.png)

To use SageMaker Processing, simply supply a Python data preprocessing script.  For example, using a SageMaker prebuilt [Scikit-learn](https://sagemaker.readthedocs.io/en/stable/amazon_sagemaker_processing.html#data-pre-processing-and-model-evaluation-with-scikit-learn) container, which includes many common functions for processing data.  There are few limitations on what kinds of code and operations you can run, and only a minimal contract:  input and output data must be placed in specified directories.  If this is done, SageMaker Processing automatically loads the input data from S3 and uploads transformed data back to S3 when the job is complete.

## Categorical variables
### Binary categorical

If your dataset contains a feature, which is a binary variable. It is possible to change the encoding from `'Y'` and `'N'` to `1` and `0`. This can be done using [`map`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.map.html), it expects either a function to apply to that column or a dictionary to look up the correct transformation.

```python
new_data['column_name'] = data['column_name'].map({'Y':1, 'N':0})
```

### Ordinal categorical

Same as above, but encoding many categorical variables. This is ideal if the feature can be thought of as an ordinal categorical variable.

```python
new_data['column_name'] = df['column_name'].map({'Poor':1, 'Fair':2, 'Average':3, 'Good':4, 'Very Good':5})
```

### Nominal categorical

TBA.

## Scaling
### Feature scaling

TBA.

## Hyperparameter optimisation

TBA.

```python
sess = sagemaker.Session()

xgb = sagemaker.estimator.Estimator(container,
                                    role, 
                                    instance_count=1, 
                                    instance_type='ml.m6.xlarge',
                                    output_path='s3://{}/{}/output'.format(bucket, prefix),
                                    sagemaker_session=sess)
xgb.set_hyperparameters(max_depth=5,
                        eta=0.2,
                        gamma=4,
                        min_child_weight=6,
                        subsample=0.8,
                        silent=0,
                        objective='binary:logistic',
                        num_round=100)

xgb.fit({'train': s3_input_train, 'validation': s3_input_validation})
```

> [!NOTE]
> 
> Specifying training parameters for the estimator, includes the following:
> 1. The `xboost` algorithm[^1] container
> 2. The IAM role to use
> 3. Training instance type and count
> 4. S3 location for output data
> 5. Algorithm hyperparameters

## Summary

In summary, you can encode features that are otherwise inaccessible to the model (such as the categorical features). In these circumstances, simple techniques like one-hot encoding or ordinal encoding can go a long way. These techniques allows you to get more from the features you already had. For example, the encoding might be good; however, the pattern was difficult for the model to use. Presenting a variable in a way that makes the data available to the model is a key to the development of a high-performing model.

[^1]: An algorithm is a sequence of instructions or a set of rules that are followed to complete a task or solve a problem.

---

#pandas #sklearn #numpy #sagemaker 