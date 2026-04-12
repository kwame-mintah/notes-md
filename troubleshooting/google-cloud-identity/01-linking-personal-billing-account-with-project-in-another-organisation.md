# Linking personal billing account with project in another organisation

As I am continuing to learn and understand how Google Cloud Platform (GCP) operates. I want to separate my personal Gmail vs Organisation (Workspace) account, but use the same billing account set up within my personal account to avoid any accidental charges coming my way and remaining unpaid. With this set up, I can easily manage costs as I use my personal Gmail account more, than the one created within my own Workspace.

After a lot of misunderstanding of how linking billing accounts works, I was able to finally understand.

## Creating a project within organisation

As I am creating all my resources via terraform within the same organisation, having a billing account is an *optional* field when creating a project within terraform:

```hcl
resource "google_project" "project" {
  name                = "gcp-ace"
  project_id          = "gcp-ace-2026-${local.org_suffix}"
  folder_id           = google_folder.environment_folder.name
  auto_create_network = false
}
```

This wouldn't be an issue, but if you want to use some services within GCP, for example `container.googleapis.com` for Google Kubernetes Engine (GKE), a billing account must be linked with the project. I run into various problems after updating terraform with my billing account from my personal account:

```hcl
resource "google_project" "project" {
  name                = "gcp-ace"
  project_id          = "gcp-ace-2026-${local.org_suffix}"
  folder_id           = google_folder.environment_folder.name
  auto_create_network = false
  billing_account     = "013CCA-610538-8B2958" # Personal billing account
}
```

Each apply would result in the following error message:

```bash
14:37:58.886 STDERR terraform: │ Error: Error setting billing account "013CCA-610538-8B2958" for project "projects/gcp-ace-2026-85e267": googleapi: Error 403: The caller does not have permission
14:37:58.886 STDERR terraform: │ Details:
14:37:58.886 STDERR terraform: │ [
14:37:58.886 STDERR terraform: │   {
14:37:58.886 STDERR terraform: │     "@type": "type.googleapis.com/google.rpc.ErrorInfo",
14:37:58.886 STDERR terraform: │     "domain": "cloudbilling.googleapis.com",
14:37:58.886 STDERR terraform: │     "metadata": {
14:37:58.886 STDERR terraform: │       "permission": "billing.resourceAssociations.create",
14:37:58.887 STDERR terraform: │       "resource": "billingAccounts/013CCA-610538-8B2958"
14:37:58.887 STDERR terraform: │     },
14:37:58.887 STDERR terraform: │     "reason": "IAM_PERMISSION_DENIED"
14:37:58.887 STDERR terraform: │   }
14:37:58.887 STDERR terraform: │ ]
14:37:58.887 STDERR terraform: │ , forbidden
14:37:58.887 STDERR terraform: │ 
14:37:58.887 STDERR terraform: │   with google_project.project,
14:37:58.887 STDERR terraform: │   on main.tf line 21, in resource "google_project" "project":
14:37:58.887 STDERR terraform: │   21: resource "google_project" "project" {
14:37:58.887 STDERR terraform: │ 
14:37:58.887 STDERR terraform: ╵
```

## Checking permissions associated to caller

This error message, was very confusing to me, as I had made the assumption that I just needed to apply the correct role to the principal belonging to the organisation (`k@intuitive-judgments.com`) from my personal accounts GCP.

However, even when adding the role '[Project Billing Manager](https://docs.cloud.google.com/billing/docs/how-to/billing-access#overview-of-cloud-billing-roles-in-cloud-iam)' and applying again resulted in the same error again. So, I applied the same role on the organisational level (`intuitive-judgments.com`) to the same principle and also 'Billing Account Administrator', 'Billing Account User'. But kept giving the same error.

Instead of running via terraform, I attempted to link via the gcloud CLI, again resulting in the same error message:

```bash
❯  gcloud beta billing projects link gcp-ace-2026-85e267 --billing-account=013CCA-610538-8B2958

ERROR: (gcloud.beta.billing.projects.link) [k@intuitive-judgments.com] does not have permission to access projects instance [gcp-ace-2026-85e267] (or it may not exist): The caller does not have permission. This command is authenticated as k@intuitive-judgments.com which is the active account specified by the [core/account] property
- '@type': type.googleapis.com/google.rpc.ErrorInfo
  domain: cloudbilling.googleapis.com
  metadata:
    permission: billing.resourceAssociations.create
    resource: billingAccounts/013CCA-610538-8B2958
  reason: IAM_PERMISSION_DENIED
```

## Resolving billing account linkage in organisation project

Unbeknownst to me, a billing account created has an IAM Policy linked with it. This IAM is not accessible and/or viewable in the general product service in GCP '[IAM & Admin](https://docs.cloud.google.com/iam/docs)', so all roles that were being applied had no affect on the billing account.

You can check the IAM policy via gcloud CLI:

```bash
❯  gcloud beta billing accounts get-iam-policy 013CCA-610538-8B2958
ERROR: (gcloud.beta.billing.accounts.get-iam-policy) [k@intuitive-judgments.com] does not have permission to access billingAccounts instance [013CCA-610538-8B2958:getIamPolicy] (or it may not exist): The caller does not have permission. This command is authenticated as k@intuitive-judgments.com which is the active account specified by the [core/account] property
```

Using the GCP console on my personal account, I had to navigate to the following:

1. Log in as the **owner of the billing account** (your personal account)
2. Go to:
    - Billing → Billing account (Drop down) → Manage billing accounts
3. Select the billing account and add new permissions to the account:
    - Click Add Principal
    - Enter the Principals' email address
    - Assign the role 'Billing Account User'

![Troubleshooting billing account permissions](attachments/troubleshooting-gcp-billing-account-permissions-linkage.png)

Running the command again to view the IAM policy, I could confirm that my organisation account, had access to the billing account set up with my personal GCP:

```bash
❯  gcloud beta billing accounts get-iam-policy 013CCA-610538-8B2958
bindings:
- members:
  - user:spectrumstarcolours@gmail.com
  role: roles/billing.admin
- members:
  - user:k@intuitive-judgments.com
  role: roles/billing.user
etag: BwZPQ8WHbtQ=
version: 1
```

With this issue resolve, I was able to successfully apply the terraform changes.

---
#gcp #google
