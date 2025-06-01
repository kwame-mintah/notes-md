# DevOps Engineer Terraform Project

To prepare for the [Amazon Web Service (AWS) DevOps Professional](https://aws.amazon.com/certification/certified-devops-engineer-professional/) certification exam, I set it upon myself to create an example architecture that would make use of services that you were expected to know. With all my projects related to cloud providers, I always use an Infrastructure as Code (IaC) tool, in order to quickly create and destroy environments and avoid unnecessary costs to my account(s).

For this project, I used Terraform and AWS CloudFormation for IaC, this was intentionally done to understand configuration options needed to provision a resource and also understand [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html) approach to configuring and deploying stacks and/or stack sets.
## Approach

Below is an overview of the resources created within AWS, all terraform code can be found in [terraform-aws-certified-devops-engineer-professional](https://github.com/kwame-mintah/terraform-aws-certified-devops-engineer-professional) and CloudFormation templates used can be found in [aws-cloudformation-playground](https://github.com/kwame-mintah/aws-cloudformation-playground).

![architecture-overview](https://raw.githubusercontent.com/kwame-mintah/terraform-aws-certified-devops-engineer-professional/refs/heads/main/diagrams/proposed_aws_devops_engineer_architecture.png)

The main goal of the project was to understand the following topics that would be covered in the exam:

- [ ] CI/CD (CodePipeline, CodeBuild, CodeDeploy)
- [ ] Infrastructure as Code (CloudFormation, CDK)
- [ ] Monitoring & Logging (CloudWatch, X-Ray)
- [ ] Security & Compliance (IAM, Secrets Manager)
- [ ] Containerisation & Deployment (ECS Fargate, ECR)
- [ ] Automation (Lambda, Step Functions)
- [ ] Cost optimisation by using free-tier and on-demand minimal configurations

## Architecture Components

### Source Code Repository

AWS offers a version controlled repository. However, this service is [not available to all customers](https://aws.amazon.com/blogs/devops/how-to-migrate-your-aws-codecommit-repository-to-another-git-provider/). Although my account has access to CodeCommit, I chose not to use it and instead use a third party version control -- GitHub being my chosen option.

> After careful consideration, we have made the decision to close new customer access to AWS CodeCommit, effective July 25, 2024. AWS CodeCommit existing customers can continue to use the service as normal. AWS continues to invest in security, availability, and performance improvements for AWS CodeCommit, but we do not plan to introduce new features.

[AWS CodeConnections](https://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-CodestarConnectionSource.html) has to be created in order to connect to GitHub, to complete the connection, you will have to authorise the app to view repositories created within the account. I created [aws-fastapi-lambda-api-gateway](https://github.com/kwame-mintah/aws-fastapi-lambda-api-gateway) to contain all the code for the [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) for this project, which would perform simple Create Read Update Delete (CRUD) operations against a DynamoDB table.

### Build & Test

[AWS CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html) allows you to build and test your application in [AWS CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome.html). This service can be a bit confusing at first, as it acts as its own *pipeline*, where a build specification has to be provided. You don't re-use the same CodeBuild project, but have to create a new one, if a different build specification is needed -- although you can override a build specification in CodePipeline, it can be quite difficult to maintain.

For the project a [simple build specification](https://raw.githubusercontent.com/kwame-mintah/terraform-aws-certified-devops-engineer-professional/refs/heads/main/templates/buildspecs/buildspec_python_pytest.yml) was created, to run Python unit tests found within the source repository, using Pytest and export test results into a report group. Because only one CodeBuild project was created, this was the only build specification. However, could have put the file within the source repository, allowing for an easy override if needed.

### Container Registry

[Amazon Elastic Container](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)  is an image registry, that will be used for storing Docker images built via AWS CodePipeline. The pipeline would build the Docker image using the Dockerfile in the source repository and push the build artefact into the registry created via Terraform. Each image would be tagged using the git commit or the newly created tag that triggered the pipeline.

### Deployment

[AWS CodeDeploy](https://docs.aws.amazon.com/codedeploy/latest/userguide/welcome.html) automates application deployments to Amazon EC2 instances, on-premises instances, serverless Lambda functions, or Amazon ECS services. No EC2 instances or ECS service will provisioned, to help keep the project as simple, deploying a containerised application will require additional work to make accessible over the internet.

An alternative approach was taken, where a CloudFormation template was created, to created and/or update a Lambda function. CodePipeline would invoke another pipeline, passing in necessary variables to to start the pipeline. This is because a pipeline can only have one source and I wanted to re-use a template stored in another repository, so two pipelines needed to be created.

### Infrastructure as Code

As mentioned earlier, both Terraform and AWS CloudFormation are used to provision resources within AWS. For example, Terraform was used to provision a stack using a CloudFormation template. If a template has defined outputs, these can be used elsewhere within Terraform in the code snippet below, the DynamoDB table name is an output of a CloudFormation template, so can be referenced in Terraform elsewhere e.g. setting an environment variable for Lambda:

```terraform
aws_cloudformation_stack.dynamodb_table_stack.outputs["TableName"]
```

AWS CodePipeline can also deploy CloudFormation templates, given required inputs for the template have been passed across.

> [!IMPORTANT]
> If an AWS CodePipeline provisioned by Terraform is used to create
> CloudFormation stack as part of the pipeline. During a Terraform destroy, the stack
> will remain along with any resources created, requiring manual deletion.

### Monitoring & Logging

[Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) monitors AWS resources and the applications run on AWS in real time. CodeBuild and AWS Lambda output their logs into a CloudWatch log group, allowing for metrics and dashboards to be created.

### Security & Secrets Management

[AWS Identity and Access Management (IAM)](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) is a web service that helps securely control access to AWS resources. With IAM, you can manage permissions that control which AWS resources users can access. For this project, several IAM roles were created to allow AWS resources to communicate with each other.

CodePipeline had an IAM role created to allow permission to CodeConnections, CodePipeline, CodeBuild, S3, ECR, CloudWatch logs and CloudFormation. The pipeline would also be allowed to assume roles from other services in order to successfully run, for example CodeBuild.

CloudFormation IAM role to allow permission to create all resources stated in the template being run, for example allow permissions for [Amazon API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html) so the resource can be created and/or destroyed.

The Lambda function deployed, has a role created as part of CloudFormation template, this role allows the Lambda necessary permissions against the specified DynamoDB table.

[AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)  helps manage, retrieve, and rotate database credentials, application credentials, OAuth tokens, API keys, and other secrets throughout their lifecycle. Unfortunately for this project the database chosen DynamoDB does not have any credentials associated, so there was no need to use this service, however if an [Amazon Relational Database Service (Amazon RDS)](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html) was deployed, Secrets Manager would be used.

### Automation

AWS Lambda is used to run code without provisioning or managing servers. The Lambda function deployed will be invoked by an API Gateway event. To start a [FastAPI](https://fastapi.tiangolo.com/) service which will route the endpoint being called accordingly to perform a CRUD operation against the DynamoDB table supported by [mangum](https://github.com/Kludex/mangum).

![aws-lambda-function-web-console-overview](https://raw.githubusercontent.com/kwame-mintah/aws-fastapi-lambda-api-gateway/refs/heads/main/docs/aws-lambda-function-overview.png)

---
#aws #aws-devops #dynamodb #terraform #dop-c02
