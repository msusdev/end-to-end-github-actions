---
title: Deploy applications to Azure using GitHub Actions
author: Part 3 of 3 in the [Build end-to-end CI/CD capabilities directly in GitHub](https://github.com/MSUSDEV/end-to-end-github-actions) series
---

# Deploy applications to Azure using GitHub Actions

## Overview

### About us

Presenter Name

☁️ *Presenter Title*

> For questions or help with this series: <msusdev@microsoft.com>

All demos and source code available online:

> <https://github.com/msusdev/end-to-end-github-actions>

## Setting the scene

### Series roadmap

* ~~Session 1:~~
  * ~~Introduction to GitHub Actions~~
* ~~Session 2:~~
  * ~~Continuous integration using GitHub Actions~~
* **Session 3**:
  * **↪️ Deploy applications to Azure using GitHub Actions**
  
### Agenda

1. Set-up Azure service principal and credentials
1. Deploy Azure Container Instance using ARM template
1. Deploy to App Service using compressed folder
1. Deploy to App Service directly

## Time to deploy to Azure from GitHub Actions

## Demo: *Automating Azure Deployment*

::: notes

### Set-up Azure login

1. Get service principal

    ```sh
    az ad sp create-for-rbac --name ghasp --role contributor --scopes /subscriptions/<subscription-id> --sdk-auth
    ```

1. Add result JSON object as a GitHub secret named **AZURE_CREDENTIALS**

1. Add subscription ID as a GitHub secret name **AZURE_SUBSCRIPTION_ID**

1. Add new job

    ```yml
    deploy-azure-containers:
      name: Deploy containers to Azure
      runs-on: ubuntu-latest
      needs: build-container
      steps:
    ```

1. Login to Azure

    ```yml
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

### Deploy ACI to resource group

1. Checkout code

    ```yml
    - name: Checkout code
      uses: actions/checkout@v2
    ```

1. Add ARM template to project (<https://gist.github.com/seesharprun/63ad396f4855aee5130557ab59159d1c>)

1. Add ARM deployment step

    ```yml
    - name: Deploy ACI instance
      uses: azure/arm-deploy@v1
      with:
        subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
        resourcegroupName: Production
        template: azuredeploy.json
    ```

### Deploy to Azure Web App container

1. Deploy Web App in the Azure portal

1. Create an environment variable for the web app's name

    ```yml
    env:
      WEB_APP_NAME: <web-app-name-here>
    ```

1. Add Web App container deployment step

    ```yml
    - name: Deploy Web App container
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.WEB_APP_NAME }}
        images: msusdev/webnext
    ```

### Deploy to Azure Web App using code

1. Add another new job

    ```yml
    deploy-azure-manual:
      name: Deploy code directly to Azure
      runs-on: ubuntu-latest
      needs: build-project
      steps:
    ```

1. Create an environment variable for the web app's name

    ```yml
    env:
      WEB_APP_NAME: <web-app-name-here>
    ```

1. Download artifact

    ```yml
    - name: Download site content
      uses: actions/download-artifact@v2
      with:
        name: site-build
    ```

1. Login to Azure

    ```yml
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Deploy code to App Service

    ```yml
    - name: Deploy Web App code
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.WEB_APP_NAME }}
        package: .
    ```

### Deploy to Azure Static Web Apps using alt workflow

1. Create an Azrue Static Web App using the portal

:::

## Review

### Agenda (revisted)

1. Set-up Azure service principal and credentials
1. Deploy Azure Container Instance using ARM template
1. Deploy to App Service using compressed folder
1. Deploy to App Service directly

### Links

* <https://docs.microsoft.com/azure/app-service/deploy-container-github-action>
* <https://github.com/azure/actions-workflow-samples>
* <https://github.com/azure/login>
* <https://github.com/azure/arm-deploy>
* <https://github.com/azure/webapps-deploy>
* <https://gist.github.com/seesharprun/63ad396f4855aee5130557ab59159d1c>
* <https://gist.github.com/seesharprun/6e969f52a242a9c4681524df51b94ea4>
