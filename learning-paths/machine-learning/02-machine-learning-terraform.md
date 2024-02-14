# Machine Learning Terraform

Resources created for the AWS machine learning pipeline. The project will utilise [Terragrunt](https://terragrunt.gruntwork.io/) ([[01-using-terragrunt-with-terraform]]).

## SageMaker Notebook Instance

The notebook instance requires permission to call other services include SageMaker and S3. Under the hood is an EC2 instance and it is possible to run scripts on start of the instance similar to [`remote-exec`](https://developer.hashicorp.com/terraform/language/resources/provisioners/remote-exec).

```terraform
resource "aws_sagemaker_notebook_instance_lifecycle_configuration" "lifecycle" {
  name      = "foo"
  on_create = base64encode("echo foo")
}
```

So, its possible to copy contents of an S3 bucket into the instance on start up into a specific directory e.g. `/home/ec2-user/SageMaker/JupyterNotebooks*.ipynb`

```shell
#!/bin/bash

sudo -u ec2-user -i <<'EOF'
aws s3 cp s3://us-west-1-tcprod/courses/ILT-TF-200-MLDWTS/v1.4.0.prod-f7c13f78/lab-1/scripts/ /home/ec2-user/SageMaker/ --recursive --exclude "*" --include "Lab1*.ipynb"
chown ec2-user:ec2-user /home/ec2-user/SageMaker/Lab1*.ipynb

aws s3 cp s3://us-west-1-tcprod/courses/ILT-TF-200-MLDWTS/v1.4.0.prod-f7c13f78/lab-1/scripts/ /home/ec2-user/SageMaker/ --recursive --exclude "*" --include "PythonCheatSheet*.ipynb"
chown ec2-user:ec2-user /home/ec2-user/SageMaker/PythonCheatSheet*.ipynb

# Removing output from the notebook.
for notebook in /home/ec2-user/SageMaker/Lab1*.ipynb
do
  jupyter nbconvert --ClearOutputPreprocessor.enabled=True --inplace "${notebook}"
  sed -i 's/ml-pipeline-bucket/<LabBucketName>/g' "${notebook}"
done
EOF
```

Cont. [[03-sagemaker-notebook-instance]]

## Task list

- [x] Create S3 bucket(s) to contain training dataset, output of training and model created
- [x] CloudWatch log group to monitor
  - [x] SageMaker NoteBook Instance debugging and process reporting (/aws/sagemaker/NotebookInstances). ARN example: `log-group:/aws/sagemaker/NotebookInstances:*`
  - [x] SageMaker training jobs (/aws/sagemaker/TrainingJobs). ARN example: `log-group:/aws/sagemaker/TrainingJobs:*`
  - [x] SageMaker endpoints (/aws/sagemaker/Endpoints/linear-learner). ARN example: `log-group:/aws/sagemaker/Endpoints/linear-learner:*`
- [x] Training job, this needs the AmazonSageMakerFullAccess IAM policy IAM. (Unsure about this resources, as training jobs need dataset that has already preprocessed... maybe.)[^1]
  - [x] Seems that the SageMaker notebook Instance can create a new training job, so will need to have the necessary permission.
- [ ] Endpoint(s) [^2] (needs a model ready to deploy)
  - [ ] Develop environment (Provisioned)
  - [ ] Staging environment (Provisioned)[^3]
  - [ ] Production (Serverless?)

## Difference between SageMaker notebooks and Studio

There are two tools for machine learning within AWS, SageMaker notebook instance and SageMaker studio. At the time writing, there is only a terraform resource for SageMaker notebook instance ([aws_sagemaker_notebook_instance](https://registry.terraform.io/providers/hashicorp/aws/5.17.0/docs/resources/sagemaker_notebook_instance)) and it seems it is not be possible to create a SageMaker studio. Below is a brief description of both tools.

For learning purposes, it is best to use the SageMaker notebook instance to better understand, what is needed to take a model into production. This may not be the case, but is my current interpretation of both tools.

### Amazon SageMaker notebook instance

An Amazon SageMaker notebook instance is a machine learning (ML) compute instance running the Jupyter Notebook App. SageMaker manages creating the instance and related resources. Use Jupyter notebooks in your notebook instance to prepare and process data, write code to train models, deploy models to SageMaker hosting, and test or validate your models[^4].

### Amazon SageMaker Studio

Amazon SageMaker Studio is an integrated development environment (IDE) for machine learning (ML) that lets you easily build, train, debug, deploy and monitor your machine learning models. Amazon SageMaker Studio provides a single unified interface for all the tools you need to take your models from experimentation to production and boost your productivity[^4].

## Difference between SageMaker Endpoint types?

In general you will use shadow variants to test and validate your model before production using a smaller portion compared to production[^5]. I have been told that in some AI / ML projects when data scientist deem a model is suitable for usage, it will be deployed to an environment. This means when a model is ready it can be copied to the appropriate location and deployed.

## Resources create while using notebooks

Users are able to create new processing and training jobs and these are not managed by Terraform. When imitating one of these jobs, the name is always unique e.g. `xgboost-YYYY-MM-DD-HH-MM-SS-SSS` at the time of writing these jobs cannot be deleted via the console or CLI. So, beware of what permissions are given to users as this can leave lingering resources when destroying the environment via Terraform.

## Handling model deployment to environments

After Data Scientists have a model they are happy with it will need to be deployed to other environments for monitoring, because the model output is placed into an S3 bucket. If there are different environments (AWS Accounts), the model can be copied across the environment and used to create new inferences (endpoints).

## Vulnerability scans ignored

The following checks from [`checkov`](https://www.checkov.io/) and [`tfsec`](https://github.com/aquasecurity/tfsec) have been ignored, reasoning has been provided in the table below. Most will need some time to address and how the main focus is to get _something_ working, so these issues will be addressed later.

### checkov

| ID          | Description                                                                                       | Reason for ignore                                                                                                                                                                            |
| ----------- | ------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CKV2_AWS_61 | Ensure that an S3 bucket has a lifecycle configuration                                            | no expectation to delete objects in the bucket.                                                                                                                                              |
| CKV2_AWS_62 | Ensure S3 buckets should have event notifications enabled                                         | no lambda function, sns topic or sqs queue exits for notifications purposes.                                                                                                                 |
| CKV_AWS_109 | Ensure IAM policies does not allow permissions management / resource exposure without constraints | root account needs access to resolve error, the new key policy will not allow you to update the key policy in the future.                                                                    |
| CKV_AWS_111 | Ensure IAM policies does not allow write access without constraints                               | root account needs access to resolve error, the new key policy will not allow you to update the key policy in the future.                                                                    |
| CKV_AWS_122 | Ensure that direct internet access is disabled for an Amazon SageMaker Notebook Instance          | to train or host models from a notebook, you need internet access. no [NAT gateway](https://docs.aws.amazon.com/sagemaker/latest/dg/appendix-notebook-and-internet-access.html) created yet. |
| CKV_AWS_144 | Ensure that S3 bucket has cross-region replication enabled                                        | no need to copy objects to another bucket in a different region out of scope.                                                                                                                |
| CKV_AWS_356 | Ensure no IAM policies documents allow “\*” as a statement’s resource for restrictable actions    | root account needs access to resolve error, the new key policy will not allow you to update the key policy in the future.                                                                    |

### tfsec

| ID                           | Description | Reason for ignore                                                        |
| ---------------------------- | ----------- | ------------------------------------------------------------------------ |
| aws-iam-no-policy-wildcards  | TBD         | Ignored as the SageMaker notebook needs access to a lot of actions.      |
| aws-vpc-no-public-egress-sgr | TBD         | SageMaker needs access to the internet to trigger training jobs (**?**). |

### Task list

- [ ] Create VPC log (<https://docs.paloaltonetworks.com/prisma/prisma-cloud/prisma-cloud-code-security-policy-reference/aws-policies/aws-logging-policies/logging-9-enable-vpc-flow-logging>, <https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/flow_log>)

[^1]: Potentially have a training job that are ready in stable environments, like staging and production?
[^2]: There are two types of endpoints: provisioned or serverless. Provisioned allows for a production and shadow variant, while serverless only allows production.
[^3]: Potentially set up CI/CD for copying the newest model from develop account to staging account. Maybe a new endpoint configuration each time...
[^4]: Which tool is better? SageMaker Studio Vs SageMaker Notebook Instance by [Salman Anwaar](https://medium.com/@salmananwaar1127/which-tool-is-better-sagemaker-studio-vs-sagemaker-notebook-instance-6f64fd545b7f).
[^5]: Answer to my question "When deploying your endpoint for your model, what is the difference between production and shadow variants?" during SageMaker Immersion Day.

---

#aws #terraform #mlops #checkov #tfsec #sagemaker
