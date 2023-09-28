# Using Terragrunt with Terraform

[Terragrunt](https://terragrunt.gruntwork.io/) is a thin wrapper that provides extra tools for keeping your configurations DRY, working with multiple Terraform modules, and managing remote state.

## Ensure Terraform is run in the correct account (AWS)

The [`aws`](https://registry.terraform.io/providers/hashicorp/aws) provider allows you to configure a list of allowed AWS accounts IDs with [`allowed_account_ids`](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#allowed_account_ids) argument. Using the [`generate`](https://terragrunt.gruntwork.io/docs/reference/config-blocks-and-attributes/#generate) block in Terragrunt allows you to generate a file in the terragrunt working directory (where terraform is called). 

```hcl
# When using this terragrunt config, terragrunt will generate the file "provider.tf" with the aws provider block before
# calling to terraform. Note that this will overwrite the `provider.tf` file if it already exists.
generate "provider" {
  path      = "provider.tf"
  if_exists = "overwrite"
  contents = <<EOF
provider "aws" {
  region              = "${local.aws_region}$"
  allowed_account_ids = ["${local.aws_account_id}"]
}
EOF
}
```

> [!NOTE]  
>
> Although, not all terraform providers have an argument similar to AWS. Generating the `provider.tf` can still be used just be mindful of how the provider is configured.

> [!NOTE]  
>
> You may need to clear your `.terragrunt-cache` directory, if you recently added the
> generation of the `provider.tf`. This is because multiple providers may exist within your cache. OR use `if_exists = overwrite_terragrunt` instead overwriting the existing file.

## Configuring remote state (S3)

Storing the Terraform state remotely with most providers, requires you to manually create the storage location. For example, with Microsoft Azure you have to create the storage container 
and then run a [`-migrate-state`](https://developer.hashicorp.com/terraform/cli/commands/init#backend-initialization) afterwards. I have previously used terraform to [create](https://github.com/kwame-mintah/terraform-azure-ai-engineer-associate/blob/cbe7f82e779a29bd46fd2c6def1ae109c856f3a4/tfstate-storage.tf) the storage account and then run the migration afters.

Terragrunt can create a S3 bucket and DynamoDB table as part of initialisation. To do this, create a `terragrunt.hcl` with the root directory of your project.

```hcl
remote_state {
  backend = "s3"
  config = {
    encrypt        = true
    bucket         = "tfstate-s3-bucket-name"
    key            = "${path_relative_to_include()}/terraform.tfstate"
    region         = "eu-west-2"
    dynamodb_table = "lock-table"
    s3_bucket_tags = {
      "Tag" = "Name"
    }
  }
  generate = {
    path      = "backend_override.tf"
    if_exists = "overwrite_terragrunt"
  }
}
```

> [!NOTE] 
>
> The account being used to deploy the Terraform configuration, must have the necessary permissions to create a S3 Bucket and DynamoDB table with the AWS account.

---

#aws #terraform #terragrunt #s3 #dynamodb