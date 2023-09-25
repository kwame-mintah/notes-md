# Machine Learning Operations (MLOps)

#### Goal

Understand how to use the machine learning (ML) pipelines to solve a real business problem in a project environment. Will understand each phase of the the pipeline and complete a project to solve a business problem. While using the AWS Cloud Infrastructure. 

#### Todo

- [x] Create terraform infrastructure [repository](https://github.com/kwame-mintah/terraform-aws-machine-learning-pipeline)
- [ ] Provision resources needed for Amazon SageMaker
	- See [[02-machine-learning-terraform]] for resources created.
- [ ] Machine Learning life cycle (MLCL)
	- [ ] Framing ML problems from business goals
	- [ ] Access, prepare and process data for the model
	- [ ] Architect ML solution and develop ML models
	- [ ] Deploy ML models
	- [ ] Iterate - Monitor, optimise and maintain the performance of the model
 - [ ] Create user interface
#### Tech stack

- Github
	- Github Actions (CI/CD)
- Amazon Web Services
	- SageMaker
	- S3
	- IAM
- Terraform
	- [AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)

#### Steps

- Preparing your Amazon SageMaker notebook
- Downloading data from the internet into Amazon SageMaker
- Investigating and transforming the data so that it can be fed to Amazon SageMaker algorithms
- Estimating a model
- Evaluating the effectiveness of the model
- Setting the model up to make ongoing predictions

---

#aws #mlops #mlcl