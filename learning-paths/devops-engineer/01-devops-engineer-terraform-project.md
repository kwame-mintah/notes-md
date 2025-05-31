# DevOps Engineer Terraform Project

For preparing for the Amazon Web Service (AWS) DevOps Professional, I set it upon myself to create an example architecture that would make use of most of the services that you were expected to know. As with all my projects related to cloud providers, I always use an Infrastructure as Code (IaC) tool, in order to quickly spin and destroy environments and avoid unnecessary costs to my account(s).

For this project, I used Terraform and AWS CloudFormation for IaC, this was intentionally done to understand configuration options needed to provision a resource and to also understand AWS CloudFormation approach to writing stacks and/or stack sets.
## Approach

Below is an overview of the resources created within AWS, all terraform code can be found in [terraform-aws-certified-devops-engineer-professional](https://github.com/kwame-mintah/terraform-aws-certified-devops-engineer-professional) and CloudFormation templates used can be found in [aws-cloudformation-playground](https://github.com/kwame-mintah/aws-cloudformation-playground).

![architecture-overview](https://raw.githubusercontent.com/kwame-mintah/terraform-aws-certified-devops-engineer-professional/refs/heads/main/diagrams/proposed_aws_devops_engineer_architecture.png)

The main goal of the project was to understand the following topics that would be covered in the exam:

- [ ] CI/CD (CodePipeline, CodeBuild, CodeDeploy)
- [ ] Infrastructure as Code (CloudFormation, CDK)
- [ ] Monitoring & Logging (CloudWatch, X-Ray)
- [ ] Security & Compliance (IAM, Secrets Manager)
- [ ] Containerization & Deployment (ECS Fargate, ECR)
- [ ] Automation (Lambda, Step Functions)
- [ ] Cost optimization by using free-tier and on-demand minimal configurations

## Architecture Components

### Source Code Repository

AWS offers a version controlled repository. However, this service is [not available to all customers](https://aws.amazon.com/blogs/devops/how-to-migrate-your-aws-codecommit-repository-to-another-git-provider/). Although my account has access to CodeCommit, I chose not to use it and instead use a third party version control -- GitHub being my chosen option.

> After careful consideration, we have made the decision to close new customer access to AWS CodeCommit, effective July 25, 2024. AWS CodeCommit existing customers can continue to use the service as normal. AWS continues to invest in security, availability, and performance improvements for AWS CodeCommit, but we do not plan to introduce new features.

[AWS CodeConnections](https://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-CodestarConnectionSource.html) has to be created in order to connect to GitHub, to complete the connection, you will have to authorise the app to view repositories created within the account. I created [aws-fastapi-lambda-api-gateway](https://github.com/kwame-mintah/aws-fastapi-lambda-api-gateway) to contain all the code for the Lambda for this project, which would perform simple CRUD operations against a DynamoDB table.

### Build & Test

[AWS CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html) allows you to build and test your application in [AWS CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome.html). This service can be a bit confusing at first, as it acts as its own *pipeline*, where a build specification has to be provided. You don't re-use the same CodeBuild project, but have to create a new one for each one that uses a different build specification -- although you can override a build specification in CodePipeline, it can be quite difficult to maintain.

For the project a [simple build specification](https://raw.githubusercontent.com/kwame-mintah/terraform-aws-certified-devops-engineer-professional/refs/heads/main/templates/buildspecs/buildspec_python_pytest.yml) was created, to run Python unit tests within the source repository, using Pytest and export test results into a report group. Because only one CodeBuild project was created, this was the only build specification made. However, could have put the file within the source repository, allowing for an easy override if needed.

### Container Registry

[AWS Elastic Container](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html) registry is an image registry, that will be used for storing Docker images built via AWS CodePipeline. The pipeline would build the Docker image using the Dockerfile within the source repository and push the artefact into the registry created. Each image would be tag using the git commit or the newly created tag that triggered the pipeline.

### Deployment

[AWS CodeDeploy](https://docs.aws.amazon.com/codedeploy/latest/userguide/welcome.html) automates application deployments to Amazon EC2 instances, on-premises instances, serverless Lambda functions, or Amazon ECS services. No EC2 instances or ECS service will provisioned, to help keep the project as simple as possible, because deploying a containerised application will require additional work to make accessible over the internet.

---
#aws #aws-devops #dynamodb #terraform
