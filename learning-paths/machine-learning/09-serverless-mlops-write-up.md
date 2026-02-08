# Serverless MLOps Write Up

The purpose of working with AWS to attempt to achieve [[07-automated-machine-learning]], specifically MLOps level 2. Was to understand the flow and potentially find suitable projects to use what was learned. As we continue to utilise machine learning models for assistance in completing tasks, I believe it is important to understand the process, as this is not a *one size fits all* approach.

I will discuss the resources created in my journey to AWS MLOps level 2.

## Terraform

I created Terraform code to deploy various resources to my AWS account, this was done  ad-hoc, as I was not sure what resources were needed, a lot of AWS services tend to overlap with their capabilities.

The repository **[terraform-aws-machine-learning-pipeline](https://github.com/kwame-mintah/terraform-aws-machine-learning-pipeline)** contains all the code for creating the following resources:

- Elastic Registry
  - For storing docker images to be referenced for lambda deployment.
- IAM Roles
  - IAM roles for different services to assume, being granted the relevant permissions.
  - GitHub actions required an IAM role to assume, to push docker images to ECR.
- S3 Buckets
  - Contained training data and training jobs output.
  - Lambda functions had various event(s) attached to be used as triggers e.g. Object created.
- SageMaker
  - Created a SageMaker notebook instance, unsure still if this was needed. The intention was create Notebooks for various scientist to use for training.
- Simple Queue Service
  - Lambda function(s) would place messages onto the queue, to be picked up and processed by another function.
- Cloudwatch Metric Alarm
  - This metric alarm was set to see if there was any errors, when invoking the model output from training jobs. With the intention to alert scientist that models were not performing as expected and need to be investigated.

## Lambda functions

In an attempt to break down the Auto MLOps process, I created various lambda functions, working together to invoke SageMaker.

The goal was to make them lightweight and easy to follow. Essentially, code that could have originated from a Jutypter notebook, but decoupled from the notebook as much as possible.

### [aws-lambda-data-preprocessing](https://github.com/kwame-mintah/aws-lambda-data-preprocessing/tree/main)

This lambda function is triggered, on the arrival of new test data within a S3 Bucket. As we get new data, we should be performing continuous training and outputting new models, because data is always changing.

The lambda function expected the data to follow a *specific* format and used Pandas for manipulation, such as dropping columns and/or converting columns to different formats.

Pre-checks were implemented to ensure that data was only processed once. Using S3 object tagging, and checking the tag set, only new information would be processed. At the end of preprocessing the data would be output into another S3 bucket.

### **[aws-lambda-model-training](https://github.com/kwame-mintah/aws-lambda-model-training)**

With the data pre-processed and placed into an S3 bucket, means that the data is ready for training. Using Pandas to split into training, validation and test data. Once split, the data would be re-uploaded into the pre-processing bucket initially retrieved from.

The next step was to initiate the training job. As this was my first project, `xgboost` algorithm was used, the function written could have been more flexible to support other algorithms, but due to lack of knowledge, would not be able to know which parameters were accepted.

With the various inputs provided, e.g. training data location, output bucket etc. This would trigger the SageMaker training job to start, without waiting for it to finish.

### **[aws-lambda-model-deployment](https://github.com/kwame-mintah/aws-lambda-model-deployment)**

The next step is to deploy the model output from the training job. Once again, because the model is placed into a S3 bucket the lambda would be triggered by this event.

It is necessary to create a model within SageMaker, so the model arn, can be referenced during serverless deployment. Which requires picking the same *algorithm* that the data was trained on, also allowing for various container configurations.

With the model now created within SageMaker, a serverless endpoint configuration is then created, include configurations for e.g. max concurrency, memory size etc. As the configuration is now created, it can now be referenced, when creating the serverless endpoint.

With the endpoint now available, the lambda will get the test data location, by using the object key (usually `/**/model.tar.gz`), this is used to infer the test data location, by checking the the tags for the training job and parsing the S3 URL.

Lastly, a message is placed onto a queue, containing the endpoint name, test data bucket name and key.

### **[aws-lambda-model-evaluation](https://github.com/kwame-mintah/aws-lambda-model-evaluation)**

With the message now placed onto the queue, this lambda function would process the message.

Before attempting to invoke the endpoint, a check is performed to ensure the endpoint status is `InService`,  if the endpoint is still in `Creating` status, will sleep for 15 seconds and check the status again. The status could change during this period e.g. `OutOfService`, `Updating` etc.

Once the endpoint is `InService` status, using SageMakers Predictors, providing the endpoint name as an argument. The test data is then downloaded from the S3 bucket. Numpy is used to split the rows and each one is prepared to be used as part of the payload.

With the response returned, a confusion matrix metrics is created and stored within a S3 bucket, for a manual review. Ideally, a baseline metric would have been set, allowing for models that perform badly to be disregarded.

## Serverless framework

In order to deploy the mentioned lambda functions, the serverless frame work was used to define, each lambda and IAM role statements, allowing permissions to relevant resources that each lambda would be interacting with.

GitHub actions, would read the relevant GitHub repositories to retrieve their tags, which correlated to their ECR tag. The repository [aws-automlops-serverless-deployment](https://github.com/kwame-mintah/aws-automlops-serverless-deployment/tree/main), is intended to be used with the Terraform resources deployed, as the events needed to be attached to S3 bucket was done here.

## Summary

In summary, this project allowed me familiarise myself with various AWS SDKs and services. I do not believe, I achieved MLOps Level 2, but did get a better understanding of the ML offerings from AWS. The reason, I think I did not reach MLOps Level 2, is because I did not answer the first question with any ML project -- What is the problem I am trying to solve?

Because I did not answer this question, a lot what was done was ad-hoc, with no clear goal of validating what was created. Instead this project, should be seen as a learning experience and if this was to be done again, would utilise more of AWS services to accomplish the same thing.

---

#aws #mlops #write-up
