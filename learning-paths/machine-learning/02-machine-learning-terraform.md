# Machine Learning Terraform

Resources created for the AWS machine learning pipeline.

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


#aws #terraform #mlops 