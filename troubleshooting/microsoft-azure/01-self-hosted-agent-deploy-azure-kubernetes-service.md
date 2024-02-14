# Using Azure Kubernetes Service

Azure Kubernetes Service (AKS) simplifies deploying a managed Kubernetes cluster in Azure by offloading the operational overhead to Azure [^1]. I recently challenged myself to deploy an application to Azure Kubernetes service, using the Azure DevOps platform and create all Azure resources via Terraform.

Continuing on from [[00-self-hosted-agent-push-to-azure-container-registry]], I run into other problems using my self-hosted build agent [^2]. A couple these problems have been documented with solutions to help others.

## Problems

### Unable to find Kubernetes name spaces

In order to deploy the service to a Kubernetes cluster. I used the Azure Pipelines task [KubernetesManifest@1](https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/kubernetes-manifest-v1?view=azure-pipelines), as per security recommendation when creating the Azure Kubernetes Service the local account has to be disabled. This caused some problems during deployment that wasn't clear to myself.

The first problem encountered was the inability to create a service connection using Azure DevOps UI. Because the local account was disabled, the UI was unable to list available name spaces because it needed the local account to be enabled, but as mentioned earlier this was not recommend.

> [!NOTE]
> Creating the service connection via the Azure DevOps UI was done to test creating the connection. In the end the service connection was create via Terraform in [terraform-azurerm-kubernetes-cluster](https://github.com/kwame-mintah/terraform-azurerm-kubernetes-cluster/blob/2f4d802e0c41756fdcbbbb01469da209ad1f2280/modules/service_connections/main.tf#L12).

In order to get past this problem, you have to use the connectionType of `azureResourceManager` [^3], rather than the default `kubernetesServiceConnection` within used for KubernetesManifiest the task. Using the resource type of `azureResourceManager` requires that the following inputs are also included:

- `azureSubscriptionConnection`
- `azureResourceGroup`
- `kubernetesCluster`

### Unable to create new secrets for cluster

Part of the deployment to AKS is to pull the docker image from Azure Container Registry (ACR) which needed docker registry credentials to be created, but on first run the pipeline was unable to create a secret. As this step would fail with the following error:

```bash
##[error]error: failed to create secret secrets is forbidden: User "5f488166-6cd6-4d64-873c-668373b110aa" cannot create resource "secrets" in API group "" in the namespace "default": User does not have access to the resource in Azure. Update role assignment to allow access.
```

What needed to be done was to create an Microsoft Entra application (MEA) and assign the following role(s): `Azure Kubernetes Service Cluster Admin Role`, `Azure Kubernetes Service RBAC Admin`, `Azure Kubernetes Service RBAC Cluster Admin` and `Azure Kubernetes Service Contributor Role`. Without these role(s) modification could not be made to the cluster as Azure Role-based access control (RBAC) had been enabled via the Terraform configuration.

### Forbidden from pulling docker images from ACR

After resolving the issue with docker registry credentials not being created. The next problem encountered after was the inability to to pull the docker image from ACR. When inspect the namespace and pod(s) the events showed that there was an `ImagePullBackOff`.

```bash
 "pythonfastapik8s.azurecr.io/pythonfastapik8s:255": [rpc error: code = Unknown desc = failed to pull and unpack 
 "pythonfastapik8s.azurecr.io/pythonfastapik8s:255": failed to resolve reference "pythonfastapik8s.azurecr.io/pythonfastapik8s:255": failed to authorize: failed t
```

Initial thoughts was that the docker credentials were not created correctly for the pod and resulted in the error. However, after hours and hours of searching it was because I had configured my ACR to only allow certain IP Addresses to pull from the registry.

When deploying to AKS, a load balancer is created as part of the service and this includes Frontend public IP addresses. At the time, I was under the assumption that only my IP addresses was enough, as the self-hosted build agent was running on my own machine. But when deploying to the cluster, the originating IP address would not be my own IP, but rather the Frontend public IP address created. Including the IP addresses into the allowed range resolved the problem.

[^1]: What is [Azure Kubernetes Service](https://learn.microsoft.com/en-us/azure/aks/intro-kubernetes)?
[^2]: Build and deploy to [Azure Kubernetes Service with Azure Pipelines](https://learn.microsoft.com/en-us/azure/aks/devops-pipeline?tabs=cli&pivots=pipelines-yaml)
[^3]: Service Connection guidance for AKS customers using Kubernetes tasks by [Eric Van Wijk](https://devblogs.microsoft.com/devops/service-connection-guidance-for-aks-customers-using-kubernetes-tasks/)

---
#azure #azure-devops #azure-pipelines #k8s
