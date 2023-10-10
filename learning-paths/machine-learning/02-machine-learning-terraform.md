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
- [ ] CloudWatch log group to monitor 
	- [ ] SageMaker NoteBook Instance debugging and process reporting (/aws/sagemaker/NotebookInstances). ARN example: `log-group:/aws/sagemaker/NotebookInstances:*`
	- [ ] SageMaker training jobs (/aws/sagemaker/TrainingJobs). ARN example: `log-group:/aws/sagemaker/TrainingJobs:*`
	- [ ] SageMaker endpoints (/aws/sagemaker/Endpoints/linear-learner). ARN example: `log-group:/aws/sagemaker/Endpoints/linear-learner:*`
- [ ] Training job, this needs the AmazonSageMakerFullAccess IAM policy IAM. (Unsure about this resources, as training jobs need dataset that has already preprocessed... maybe.)[^1] 
	- [x] Seems that the SageMaker notebook Instance can create a new training job, so will need to have the necessary permission.
- [ ] Endpoint(s) [^2] (needs a model ready to deploy)
	- [ ] Develop environment (Provisioned) [^3]
	- [ ] Staging environment (Provisioned)
	- [ ] Production (Serverless?)

## Difference between SageMaker notebooks and Studio

There are two tools for machine learning within AWS, SageMaker notebook instance and SageMaker studio. At the time writing, there is only a terraform resource for SageMaker notebook instance ([aws_sagemaker_notebook_instance](https://registry.terraform.io/providers/hashicorp/aws/5.17.0/docs/resources/sagemaker_notebook_instance)) and it seems it is not be possible to create a SageMaker studio. Below is a brief description of both tools. 

For learning purposes, it is best to use the SageMaker notebook instance to better understand, what is needed to take a model into production. This may not be the case, but is my current interpretation of both tools.

### Amazon SageMaker notebook instance

An Amazon SageMaker notebook instance is a machine learning (ML) compute instance running the Jupyter Notebook App. SageMaker manages creating the instance and related resources. Use Jupyter notebooks in your notebook instance to prepare and process data, write code to train models, deploy models to SageMaker hosting, and test or validate your models[^4].

### Amazon SageMaker Studio

Amazon SageMaker Studio is an integrated development environment (IDE) for machine learning (ML) that lets you easily build, train, debug, deploy and monitor your machine learning models. Amazon SageMaker Studio provides a single unified interface for all the tools you need to take your models from experimentation to production and boost your productivity[^4].

## Difference between SageMaker Endpoint types?

In general you will use Shadow variants to test and validate your model before production with a smaller portion compared to production[^5].


[^1]: Potentially have a training job that are ready in stable environments, like staging and production?
[^2]: There are two types of endpoints: provisioned or serverless. Provisioned allows for a production and shadow variant, while serverless only allows production.
[^3]: Potentially set up CI/CD for copying the newest model from develop account to staging account. Maybe a new endpoint configuration each time...
[^4]: Which tool is better? SageMaker Studio Vs SageMaker Notebook Instance by [Salman Anwaar](https://medium.com/@salmananwaar1127/which-tool-is-better-sagemaker-studio-vs-sagemaker-notebook-instance-6f64fd545b7f).
[^5]: Answer to my question "When deploying your endpoint for your model, what is the difference between production and shadow variants?" during SageMaker Immersion Day.

---

#aws #terraform #mlops 