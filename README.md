---
page_type: sample
languages:
  - python
products:
  - azure
  - azure-redis-cache
description: "This sample creates a multi-container application in an Azure Kubernetes Service (AKS) cluster."
---

# Azure Voting App

This repo has been updated to support multi-architecture container builds (`amd64` and `arm64`) to support [Azure Open Source Labs](https://github.com/Azure-Samples/azure-opensource-labs) content.

> The original repo cna be found [here](https://github.com/Azure-Samples/azure-voting-app-redis)

This sample creates a multi-container application in an Azure Kubernetes Service (AKS) cluster. The application interface has been built using Python / Flask. The data component is using Redis.

To walk through a quick deployment of this application, see the AKS [quickstart](https://learn.microsoft.com/azure/aks/tutorial-kubernetes-prepare-app).

To walk through a complete experience where this code is packaged into container images, uploaded to Azure Container Registry, and then run in and AKS cluster, see the [AKS tutorials](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-app?WT.mc_id=none-github-nepeters).

## Using GitHub Action

This repo contains a [GitHub workflow file](./.github/workflows/docker-image.yml) to build and publish your container image to Azure Container Registry. Make sure you have deployed an Azure Container Registry with the [admin account enabled](https://learn.microsoft.com/azure/container-registry/container-registry-authentication?tabs=azure-cli#admin-account).

The admin account credentials needs to be stored as secrets in your GitHub repo. You can upload secrets by following this guide or you can follow these instructions to upload secrets to your repo using the GitHub CLI.

Using the Azure CLI, run the following commands pull out the registry credentials:

```bash
acrServer=$(az acr show --name <YOUR_ACR_NAME> --query loginServer -o tsv)
acrUsername=$(az acr credential show --name <YOUR_ACR_NAME> --query username -o tsv)
acrPassword=$(az acr credential show --name <YOUR_ACR_NAME> --query "passwords[0].value" -o tsv)
```

Using the GitHub CLI, run the following commands to set your repository secrets:

```bash
gh secret set ACR_SERVER --body $acrServer --repos <YOUR_GITHUB_ACCOUNT>/<YOUR_REPO_NAME>
gh secret set ACR_USERNAME --body $acrUserName --repos <YOUR_GITHUB_ACCOUNT>/<YOUR_REPO_NAME>
gh secret set ACR_PASSWORD --body $acrPassword --repos <YOUR_GITHUB_ACCOUNT>/<YOUR_REPO_NAME>
```

To push an image to the Azure Container Registry, create a new tag and release using the following command:

```bash
gh release create v1.0.0
```

This will kick off the GitHub Action workflow to build and publish the container image.

To validate your image, you can run the following:

```bash
az acr manifest list-metadata --registry <YOUR_ACR_NAME> --name azure-vote-front
```
