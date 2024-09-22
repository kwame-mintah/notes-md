# Feature Engineering

Feature engineering is the act of making data easier for a machine learning model to understand. It's not adding anything new but reshaping and curating the existing data to make the existing patterns more apparent. Feature engineering is the process of using domain knowledge of the data to create features that make machine learning algorithms work better than they would on a simple raw encoding.

## Feature engineering with SageMaker Processing

Amazon SageMaker Processing allows you to run steps for data pre- or post-processing, feature engineering, data validation, or model evaluation workloads on Amazon SageMaker. Processing jobs accept data from Amazon S3 as input and store data into Amazon S3 as output.

![Processing data in SageMaker](https://sagemaker.readthedocs.io/en/stable/_images/amazon_sagemaker_processing_image1.png)

To use SageMaker Processing, simply supply a Python data preprocessing script.  For example, using a SageMaker prebuilt [Scikit-learn](https://sagemaker.readthedocs.io/en/stable/amazon_sagemaker_processing.html#data-pre-processing-and-model-evaluation-with-scikit-learn) container, which includes many common functions for processing data.  There are few limitations on what kinds of code and operations you can run, and only a minimal contract:  input and output data must be placed in specified directories.  If this is done, SageMaker Processing automatically loads the input data from S3 and uploads transformed data back to S3 when the job is complete.

## Categorical variables
### Binary categorical

If your dataset contains a feature, which is a binary variable. It is possible to change the encoding from `'Y'` and `'N'` to `1` and `0`. This can be done using [`map`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.map.html), it expects either a function to apply to that column or a dictionary to look up the correct transformation.

```python
new_data["column_name"] = data["column_name"].map({"Y": 1, "N": 0})
```

### Ordinal categorical

Describes information that can be categorised and has a distinct order or ranking. Same `pandas` implementation, but encoding many categorical variables. This is ideal if the feature can be thought of as an ordinal categorical variable.

```python
new_data["column_name"] = df["column_name"].map(
    {"Poor": 1, "Fair": 2, "Average": 3, "Good": 4, "Very Good": 5}
)
```

### Nominal categorical

Nominal data is categorical data that may be divided into groups, but these groups lack any intrinsic hierarchy or order.

## Scaling
### Feature scaling

The process of transforming the numerical features of a dataset into a standardised range. It involves bringing all the features to a similar scale, so that no single feature dominates the learning algorithm. By scaling the features, we can ensure that they contribute equally to the model’s performance.

### MinMax scaling

Min-max scaling, also known as normalisation, is a technique commonly used in data preprocessing. It is used to transform numerical features into a specific range, typically between 0 and 1.

### Maxabs scaling

The maximum absolute scaling rescales each feature between -1 and 1 by dividing every observation by its maximum absolute value.

### Robust scaling

 Standardizing input variables in the presence of outliers is to ignore the outliers from the calculation of the mean and standard deviation, then use the calculated values to scale the variable.

### Normalizer

Broadly speaking, the process of converting a variable's actual range of values into a standard range of values.

## Feature engineering techniques

### t-Distributed Stochastic Neighbor Embedding (t-SNE)

t-SNE (t-distributed Stochastic Neighbor Embedding) is an unsupervised non-linear dimensionality reduction technique for data exploration and visualising high-dimensional data. Non-linear dimensionality reduction means that the algorithm allows us to separate data that cannot be separated by a straight line.

### Principal Component Analysis (PCA) algorithm

Used to examine the interrelations among a set of variables. It is also known as a general factor analysis where regression determines a line of best fit. The main goal is to reduce the dimensionality of a dataset while preserving the most important patterns or relationships between the variables without any prior knowledge of the target variables.

## Hyperparameter optimisation

Model developers explicitly define parameters to control the learning process and determine model parameter values that the algorithm uses to learn.

The prefix “hyper” in the term hyperparameters indicates their role as top-level parameters controlling the learning process. Hyperparameters exist externally in the model and cannot be estimated using the data.

### BlazingText

#### Word2Vec

| Name   | Description                                                                               |
| ------ | ----------------------------------------------------------------------------------------- |
| `mode` | The Word2vec architecture used for training. e.g. `batch_skipgram`, `skipgram`, or `cbow` |

#### Text Classification

`word_ngrams` and `vector_dim` to see how many words to look at together

| Name          | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| `word_ngrams` | The number of word n-gram features to use.                   |
| `vector_dim`  | The dimension of the word vectors that the algorithm learns. |
### Factorization Machines

| Name             | Description                                                                                                     |
| ---------------- | --------------------------------------------------------------------------------------------------------------- |
| `predictor_type` | The type of predictor. `binary_classifier`: For binary classification tasks. `regressor`: For regression tasks. |

### IP Insights

| Name                 | Description                                                |
| -------------------- | ---------------------------------------------------------- |
| `num_entity_vectors` | To set hash size, set to 2x unique entity identifiers      |
| `vector_dim`         | Size of embedding vectors, too large can cause overfitting |

### K-Means

| Name                  | Description                                                                                                                                                  |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `k`                   | The number of required clusters. Can be tricky to pick, but can use the elbow method.                                                                        |
| `extra_center_factor` | The algorithm creates K centers = `num_clusters` * `extra_center_factor` as it runs and reduces the number of centers from K to k when finalizing the model. |
| `init_method`         | Method by which the algorithm chooses the initial cluster centers.                                                                                           |

### K-Nearest Neighbors (k-NN)

| Name | Description                                                                         |
| ---- | ----------------------------------------------------------------------------------- |
| `k`  | The number of nearest neighbors. Can get diminshing returns with higher value of k. |

### Latent Dirichlet Allocation (LDA)

| Name         | Description                                                                                                                                                                                                                      |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `num_topics` | The number of topics for LDA to find within the data.                                                                                                                                                                            |
| `alpha0`     | Initial guess for the concentration parameter: the sum of the elements of the Dirichlet prior. Small values are more likely to generate sparse topic mixtures and large values (greater than 1.0) produce more uniform mixtures. |

### Linear Learner

| Name                         | Description                                                                                                        |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| `balance_multiclass_weights` | For tuning multi-class can choose `balance_multiclass_weights` so each class has equal importance in loss function |
| `learning_rate`              | The step size used by the optimiser for parameter updates.                                                         |
| `mini_batch_size`            | The number of observations per mini-batch for the data iterator.                                                   |

### Neural Topic Modeling (NTM)

| Name          | Description                         |
| ------------- | ----------------------------------- |
| `num_topics`  | The number of required topics.      |
| `feature_dim` | The vocabulary size of the dataset. |

### Random Cut Forest

| Name                   | Description                                                                                                                                                                   |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `num_trees`            | Number of trees in the forest. Helps reduce noise                                                                                                                             |
| `num_samples_per_tree` | Number of random samples given to each tree from the training data set. If you know how much of data is anomalous can set this to do a better job at finding those anomalies. |

### Semantic Segmentation

| Name                   | Description                                                                                                                                                   |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `backbone`             | The backbone to use for the algorithm's encoder component. To set the encoder, can choose `resnet-50`, `resnet-101`.                                          |
| `use_pretrained_model` | Whether a pretrained model is to be used for the backbone. `True` or `False`.                                                                                 |
| `algorithm`            | The algorithm to use for semantic segmentation. Set to `fcn` for fully convolutional networks, `psp` for pyramid scene parsing, and `deeplab` for deeplab V3. |

### Principal Component Analysis (PCA)

| Name             | Description                                                                                          |
| ---------------- | ---------------------------------------------------------------------------------------------------- |
| `algorithm_mode` | Mode for computing the principal components.                                                         |
| `subtract_mean`  | Indicates whether the data should be unbiased both during training and at inference. (unbiased data) |

### XGBoost

| Name               | Description                                                                                                                                                                                                                 |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `subsample`        | Subsample ratio of the training instance. Setting it to 0.5 means that XGBoost randomly collects half of the data instances to grow trees. This prevents overfitting.                                                       |
| `eta`              | Step size shrinkage used in updates to prevent overfitting.                                                                                                                                                                 |
| `gamma`            | Minimum loss reduction required to make a further partition on a leaf node of the tree. The larger, the more conservative the algorithm is.                                                                                 |
| `alpha`            | L1 regularization term on weights. Increasing this value makes models more conservative.                                                                                                                                    |
| `lamda`            | L2 regularization term on weights. Increasing this value makes models more conservative.                                                                                                                                    |
| `eval_metric`      | Sets optimization metric. Can set it to area under cover (AUC) if you care about false positives. Can also set it to root mean square error (RMSE) `rmse`: for regression, `error`: for classification, `map`: for ranking. |
| `scale_pos_weight` | Controls the balance of positive and negative weights. It's useful for unbalanced classes. A typical value to consider: `sum(negative cases)` / `sum(positive cases)`.                                                      |
| `max_depth`        | Maximum number of nodes to be added. Relevant only if grow_policy is set to lossguide.                                                                                                                                      |

### Configuring AWS training job

Below is an example for starting a training job for `XGBoost` and configuring hyperparameters.

> [!NOTE]
>
> Specifying training parameters for the estimator, includes the following:
>
> 1. The `xboost` algorithm[^1] container
> 2. The IAM role to use
> 3. Training instance type and count
> 4. S3 location for output data
> 5. Algorithm hyperparameters

```python
sess = sagemaker.Session()

xgb = sagemaker.estimator.Estimator(
    container,
    role,
    instance_count=1,
    instance_type="ml.m6.xlarge",
    output_path="s3://{}/{}/output".format(bucket, prefix),
    sagemaker_session=sess,
)
xgb.set_hyperparameters(
    max_depth=5,
    eta=0.2,
    gamma=4,
    min_child_weight=6,
    subsample=0.8,
    silent=0,
    objective="binary:logistic",
    num_round=100,
)

xgb.fit({"train": s3_input_train, "validation": s3_input_validation})
```

## Summary

In summary, you can encode features that are otherwise inaccessible to the model (such as the categorical features). In these circumstances, simple techniques like one-hot encoding or ordinal encoding can go a long way. These techniques allows you to get more from the features you already had. For example, the encoding might be good; however, the pattern was difficult for the model to use. Presenting a variable in a way that makes the data available to the model is a key to the development of a high-performing model.

[^1]: An algorithm is a sequence of instructions or a set of rules that are followed to complete a task or solve a problem.

---

#algorithms #pandas #sklearn #numpy #sagemaker
