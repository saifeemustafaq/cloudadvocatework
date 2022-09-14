Let us deploy a Container App with Basic Environment and no extra security and the app can be accessed publicly over the internet. In this exercise, we'll be implementing **Scenario 1** from the below diagram.

![hla](../media/hla.png "Deploy Container Apps")

**Task1**, create a Basic environment with no _Additional Security_

- Setup tools and resources for Container Apps.
- Create a Basic Environment, which will host a Container App.

**Task2**, deploy Container App in the Basic Environment

- Prepare an Azure Function App to be deployed as Container App.
- Deploy a Container App on the Basic environment using the Azure Function App image.
- Access the Container App through the public FQDN.

## Create a Basic environment

In this task, we'll build a Container App _Environment_ with Basic functionalities with no extra security and providing full public access to the Applications.

1. Run the following command to set the needed CLI variables. The variables set necessary values for cloud resources like a _Tenant_, _Resource Group_, _Location_ and _Azure Container Registry and its credentials_.

   ```bash
   tenantId="<tenantId>"
   subscriptionId="<subscriptionId>"
   resourceGroup="<resourceGroup>"
   location="<location>"
   basicEnvironment="basic-env"
   acrName="<acrName>"
   registryServer="<container_registry_server>"
   registryUserName="<container_registry_username>"
   registryPassword="<container_registry_password>"
   ```

2. Add CLI extension for Container Apps.

   ```bash
   az extension add --upgrade
   ```

3. Add Microsoft.App namespace for the Container Apps.

   ```bash
   # Register the Microsoft.App namespace
   az provider register --namespace Microsoft.App
   az provider show --namespace Microsoft.App
   ```

4. Create a Resource Group on Azure hosting the Container Apps.

   ```bash
   az group create --name $resourceGroup --location $location
   ```

5. Let us create a Simple environment with _no extra security_ for the underlying infrastructure.

   ```bash
   az containerapp env create --name $basicEnvironment --resource-group $resourceGroup \
   --location $location
   ```

## Deploy Container App in Basic Environment

Let us deploy a simple Azure Function App, which responds to Http(s) calls and returns some formatted message as response with _version no. of the image is tagged to it_.

### httpcontainerapp

1. Create a [Storage account](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) on Azure.

> [!NOTE]
>
> Azure Function app needs a Storage account to cache its state and metadata.
> [!TIP]
>
> Note down the **Connection String** of the Azure Storage account; this will be used later.

2. **Create** an Azure Function App project locally using Visual Studio Code.

> [!NOTE]
>
> Readers can create Azure Function App using any other preferred IDE as well.

3. Let us name the Function App as - **httpcontainerapp**.

4. Select **Http trigger**.

5. Open Project workspace in Visual Studio Code

- Select **local.settings.json** file in the editor.
- Modify the **AzureWebJobsStorage** environment variable, replacing its value by the _Connection String_ value from previous step.
- Select the **httpcontainerapp.cs** file in the editor and update as below.

  ```csharp
  namespace HttpContainerApps
  {
      public static class HttpContainerApps
      {
          [FunctionName("container")]
          public static async Task<IActionResult> Run(
              [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)] HttpRequest req,
              ILogger log)
          {

              log.LogInformation("C# HTTP trigger function processed a request.");

              var name = req.Query["name"];
              var response = $"Hello from Container {name}-v1.0.0";
              return new OkObjectResult(response);

          }
      }
  }
  ```

6. Run the function app locally.

   ```bash
   func start
   ```

   - With default configurations the function app would be hosted at **localhost:7071**.

   - Verify the function app endpoint (_curl_, _wget_ or a _browser_ can be used).

   ```bash
   curl http://localhost:7071/api/container?name=App
   ```

   - Check the response as below.

   ```bash
   Hello from Container App-v1.0.0
   ```

7. Run the following commands to create an Azure Container Registry.

   ```bash
   az acr create --name $acrName --resource-group $resourceGroup \
   --sku Standard --admin-enabled true
   ```

   - Setting **--admin-enabled** to true will provide us the Azure Container registry credentials:

     - The **Username** to log in to container registry.
     - The **Password** to log in to container registry.

   - Run the following command to get the credentials for the Azure Container Registry.

   ```bash
   az acr credential show -n $acrName
   ```

   > [!NOTE]
   >
   > Please note down the **Credentials** which will be used later during the creation of the Container App.

8. Use the following command to Create the Container App resource on Azure.

   ```bash
   httpImageName="$registryServer/httpcontainerapp:v1.0.0"
   azureWebJobsStorage="<Storage Connection string as needed by Azure Function>"
   revisionSuffix=rv1

   # Deploy Container App
   az containerapp create --name httpcontainerapp --resource-group $resourceGroup \
   --image $httpImageName --environment $basicEnvironment \

   # Credentials for Azure Container Registry
   --registry-login-server $registryServer --registry-username $registryUserName \
   --registry-password $registryPassword \

   #External Ingress - generates a Public FQDN
   --ingress external --target-port 80 --transport http \

   # Min/Max Replicas
   --min-replicas 1 --max-replicas 5 \

   #New Revision Suffix
   --revision-suffix $revisionSuffix

   # CPU/Memory specs; similar to resource quota requests oin K8s Deployment manifest
   --cpu 0.25 --memory 0.5Gi \

   # Secrets needed by Azure Function App; similar to K8s secrets
   --secrets azurewebjobsstorage=$azureWebJobsStorage \

   # Environment variables assigned from secrets created; similar to secretRef in K8s Deployment manifest
   --environment-variables "AzureWebJobsStorage=secretref:azurewebjobsstorage"
   ```

9. The preceding command has deployed an app for us in an Azure Container App environment. The deployment has the following properties:

- The App can be accessed from anywhere.
- The following information has been provided as parameters. This gives us flexibility to reuse these commands in different environments like Dev, Staging and Production and also on different customer sites

  - No separate **Load Balancer** is needed to maintain; Azure does it automatically.

  - **--target-port** indicates the _Container Port_; as exposed in Dockerfile and similar to **_containerPort_** in K8s Deployment manifest.

  - This Deployment also ensures a **minimum of 1 replica** and **maximum of 5 replicas** for this App
    - Azure Container Registry credentials are passed as CLI arguments
      - **--registry-login-server** - Hostname of the Azure Container Registry server.
      - **--registry-username** - Username for the Azure Container Registry server.
      - **--registry-password** - Password for the Azure Container Registry server.

- **CPU** and **Memory** is also specified. This is something you can configure as needed. Consult the Docs for limits on these values.

- **Secrets** are added as part of the Container App _Deployment_ process.

### Let us verify your deployment in Azure portal

We'll now walk through the different resources that have been deployed in Azure portal as a result of the previous commands executions.

> [!NOTE]
>
> The name of various parameters in the screenshots below are only shown as an example; not referring to any particular repository or source code.

1. Please go to **portal.azure.com**.
2. Type **Container Apps** in the search box on the top.
3. You should be able to see the Container App created earlier. It will be named as **httpcontainerapp**, if you're following the instructions so far or any other name that you had chosen while creating it.
4. Select the container app and it will open the following page.

### Container App

The Container App resource that has been created on Azure providing options for managing _Secrets, Ingress, Revision management, details of the underlying Containers_ etc. Other options like [Authentication](https://docs.microsoft.com/en-us/azure/container-apps/authentication), [Continuous Deployment](https://docs.microsoft.com/en-us/azure/container-apps/github-actions-cli?tabs=bash), [Identity](https://docs.microsoft.com/en-us/azure/container-apps/managed-identity?tabs=portal%2Cdotnet) etc. are out of scope for this module.

### Application Uri

This is the _Public FQDN_ or _Base URI_ to access the Container App resource from a browser for example.

> [!TIP]
>
> Please take a note of this FQDN as we will be using for testing the app later.
> ![conntainerapp-fqdn](../media/conntainerapp-fqdn.png "Container App - Application Uri")

### Container App Environment

The environment for the Container App. In the above case, it's a Basic environment with no extra security and Ingress is **External**. Hence all access to the Container App is **Public**. In the next exercise, we'll create a more Secured Environment providing guardrails around the Container App.

### Secrets

Secrets passed during Deployment are added here. New Secrets can be added.

![conntainerapp-secrets](../media/conntainerapp-secrets.png "Container App - Add Secrets")

Existing secrets can be edited or deleted.

![conntainerapp-secrets-edit](../media/conntainerapp-secrets-edit.png "Container App - Edit/Delete Secrets")

### Revisions

Multiple Revisions with _Name, Provisioning Status_ and _State_ are displayed. Splitting of Traffic is also possible.

![containerapp-rervision-50-50](../media/containerapp-rervision-50-50.png "Container App - Traffic Split")

### Container Details

This shows the details of the Container with information around _Image Source, Registry server, Image Tag, OS Type_ etc.

![conntainerapp-details](../media/conntainerapp-details.png "Container App - Container details")

### Environment variables

This shows the details of the **Environment Variables** used by each _Revision_ of the Container App.

![conntainerapp-env-vars](../media/conntainerapp-env-vars.png "Container App - Environment variables")

### Let us Test Access to the Container App

We'll use the FQDN of the Container App as the base URL and then test the functionality of the app. The format of the entire Container App Url hosting the function app (that is, _httpcontainerapp_) will be in the below form. Try this url using _curl_, _wget_ or on a _browser_

```bash
https://<FQDN of Container App>/api/container?name=App
```

The resulting output should be of the below form; similar to what we had while testing the function app locally

```bash
Hello from Container App-v1.0.0
```

## Conclusion

Congratulations! You've just now successfully created your first Container app on Azure. We'll extend this example in the next exercise to impose extra security measures to this Container App and make it secured.
