# Using Azure Container Registry

Azure Container Registry (ACR) is a fully managed, geo-replicated service that supports Docker and OCI images and artifacts [^1]. I recently challenged myself to deploy an application to Azure Kubernetes service, using the Azure DevOps platform and create all Azure resources via Terraform.

Due to my organisation / project being private, I had to use my own self-hosted agent to run all my pipeline jobs. Reusing my existing [repository](https://github.com/kwame-mintah/azuredevops-selfhosted-agents-docker-compose) for running a self-host build agent via docker-compose. I run into a couple of problems and have documented these problems and solutions to help others.

## Problems

### Unable to execute `docker` commands

In order to build and push the docker image to ACR. The Azure Pipelines task [Docker@2](https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/docker-v2?view=azure-pipelines&tabs=yaml) was used. However using my [Dockerfile](https://github.com/kwame-mintah/azuredevops-selfhosted-agents-docker-compose/blob/126f0f0f35e8c5e6b84b97eec6317966d2cb3f09/Dockerfile) only a small amount of packages, CLI commands are installed on my self-hosted build agent.

Because `docker` command was missing, the first build attempt failed as expected. Following the official documentation for [installing docker engine on ubuntu](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository) , I decided not to include docker and other related packages within my Dockerfile and instead added script steps in the pipeline.

```yaml
  - script: |
	  apt-get update
	  apt-get install ca-certificates curl gnupg
	  install -m 0755 -d /etc/apt/keyrings
	  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
	  chmod a+r /etc/apt/keyrings/docker.gpg

	  echo \
		"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
		$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
		tee /etc/apt/sources.list.d/docker.list > /dev/null
	  apt-get update
	workingDirectory: $(projectRoot)
	displayName: "Set up the Docker repository"

  - script: |
	  apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y --allow-change-held-packages
	  rm -rf /var/lib/apt/lists/* /var/cache/apt/archives/*
	workingDirectory: $(projectRoot)
	displayName: "Install the Docker packages"
```

This was successful, but an improvement that can be made is to check the `docker` command cannot be found, before attempting to install all the packages. At the moment, it would run these scripts on each job run. An example is:

```bash
	if command; then
		echo "command found, will skip install"
	else
		echo "command not found will install"
	fi
```

### Cannot connect to the docker daemon

Although, I had installed all the relevant packages to use the docker command. And also ensured that the service was running with `service docker start` verifying the output `Starting Docker: docker ...done.`.  Docker daemon was not running and could not successfully build and push the image to ACR.

After searching online, I found the Docker-in-Docker (DIND) strategy, were you run docker within an already containerised environment [^2]. And was a quite common strategy if one of your pipeline stages then builds an image or interacts with containers. So, was comfortable to add the following onto my [`docker-compose`](https://github.com/kwame-mintah/azuredevops-selfhosted-agents-docker-compose/blob/126f0f0f35e8c5e6b84b97eec6317966d2cb3f09/docker-compose.example.yml) file.

```yaml
volumes:
	- /var/run/docker.sock:/var/run/docker.sock
```

After making this change, the build was successful and the docker image was built and pushed to ACR.

> [!NOTE]
> 
> Using the DIND strategy means that all docker images built will be available on the host machine. If building a lot of different images, this can quickly take up space on the host machine. Ensure there is a periodic clear down of images via a garbage collector, or include a post step to remove the recently built image after successful pushing to ACR.

### Unable to pull image from ACR Quarantine

To assure a registry only contains images that have been vulnerability scanned, ACR introduced the Quarantine pattern. When a registries policy is set to Quarantine Enabled, all images pushed to that registry are put in quarantine by default. Only after the image has been verified, and the quarantine flag removed may a subsequent pull be completed [^3].

Following a DevSecOps approach meant integrating security at every phase of the software development lifecycle (SDLC). Enabling the ACR Quarantine option was quite problematic because it was  _preview_  feature. Although, I had assigned my Azure Active Directory Application, the roles "`AcrQuarantineReader`" and "`AcrPull`".

Unfortunately, in order to no longer be blocked, the feature had to be turned off. This was because the challenge was time-boxed and the pipeline did not have a stage to update the docker image in ACR to no longer be in quarantine via a PATCH request to: `https://[login-url]/acr/v1/[image]/_manifests/[digest]` with the following payload:

```json
{
	"quarantineState": "[Passed|Failed]",
	"quarantineDetails": "[json string of detailed results]"
}
```

Ideally, a image scanning tool like [trivy](https://trivy.dev/) would check the image and if the stage passes, the pipeline would make the API request to no longer quarantine the image.

### Image built on the wrong platform

When building a docker image, it's important to note what platform the docker image was built on. When using DIND you should consider the operating system (OS) that is building the image. In my case, my MacBook Pro M1 was building the docker image, which is used for deployment to Azure Kubernetes Service (AKS).

Although the service run locally, without any issues. When deployed to AKS, it failed to start with:

```bash
Exec format error: '/app/.venv/bin/python'
```

This was due to an architecture mismatch [^4], because the host machine for the self-hosted build agent was using a Apple M-series chipset which is different to both ARM and x86. AKS uses a linux distribution and the docker image needed to be built for `linux/amd64`, updating the [`Dockerfile`](https://github.com/kwame-mintah/python-fastapi-azure-k8s-cluster/blob/fc9532a7103523b8770ef03651b0c0ddea3d004a/Dockerfile#L1) with the platform resolved the problem:

```bash
FROM --platform=linux/amd64
```


[^1]:  Overview of [Azure Container Registry](https://azure.microsoft.com/en-us/products/container-registry/)
[^2]:  How (and Why) to Run Docker Inside Docker by [JAMES WALKER](https://www.howtogeek.com/devops/how-and-why-to-run-docker-inside-docker/)
[^3]: Quarantine Pattern [README.md](https://github.com/Azure/acr/blob/c8ef28e6a7af36b03c0098af8014c7af07cc1cca/docs/preview/quarantine/readme.md)
[^4]: How to Fix “[exec user process caused: exec format error](https://beebom.com/how-fix-exec-user-process-caused-exec-format-error-linux/)” in Linux

---
#macos #azure #azure-devops #azure-pipelines #azure-container-registry