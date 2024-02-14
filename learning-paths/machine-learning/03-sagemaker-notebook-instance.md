# Using SageMaker Notebook Instance

When using the SageMaker notebook instance, you will need to have a S3 Bucket and prefix, that will be used for training and model data. They should be within the same region as the notebook instance, training and hosting Additionally, an IAM role is needed to give training and hosting access to the data.

During the creation of the SageMaker notebook instance, its best to assign a role with the necessary permissions so it can used within the same context when using `sagemaker.get_execution_role()` or provide the IAM role or ARN that has the correct permissions including access to the data within S3 bucket.

If more than one role is required for the notebook instances, training, and/or hosting, replace the `get_execution_role()` call with the appropriate full IAM role ARN string(s).

Because the notebook instance has access to the internet (alternatively copy the data from the S3 bucket into the instance `aws s3 cp s3://data-location/data.zip` can be achieved using [`subprocess`](https://docs.python.org/3/library/subprocess.html) within the Juypter notebook since, but it is best to use the [boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) client), you can download the data from the internet through `wget`:

```shell
!wget https://archive.ics.uci.edu/ml/machine-learning-databases/00222/bank-additional.zip
!unzip -o bank-additional.zip
```

At this stage, the data is stored within the notebook instance and can use packages like `numpy` and `pandas` within Juypter notebook for various preprocessing ([[04-data-preprocessing]]) of the data.

> [!IMPORTANT]
> If you upload an existing notebook into the instance having previously run through the cells and would like to continue where you left. You will have to make sure you use the option to run through all the cells above. Or there will be missing variables, DataFrames etc.
>
> Select 'Cell' then 'Run All Above'

#### Breakdown [^1]

1. Import data
2. Data preprocessing
3. Train model (training job with SageMaker) using `xgboost` (Amazon SageMaker's implementation of XGBoost from ECR) with splitting the data set for training and validation purposes
4. Finished Model trained is stored in S3 bucket
5. Deploy endpoint using model (stored in S3 bucket)
6. Make a request to the endpoint to test the model.

[^1]: This is my current understanding of the process.

---

#aws #sagemaker #s3 #juypter
