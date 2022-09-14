Let us deploy a Container App with Secured Environment, restricting access from internet. In this exercise, we'll be implementing **Scenario 2** from the below diagram.

![hla](../media/hla.png "Deploy Container App")

**Task1**, create a Secured Environment restricting access within _Environment_

- Create a Secured Environment for Container Apps providing a Virtual Network.
- Ensure maximum restriction and security by configuring it to be accessible from within the environment only.
- Deploy a Container App on the Secured environment.
- Check that the Access to the Container App is _blocked_ over the **Internet** or even from within a peered **Virtual Network**.
- Remove the Container App and its environment as we would re-create it a more relaxed environment.

**Task2**, create a Secured Environment restricting access from within _Virtual Network_

- Create a Secured Environment for Container Apps providing a Virtual Network.
- Provide more flexibility to allow access from same or peered virtual networks.
- Deploy a Container App on the new Secured but less Restricted environment.
- Deploy a Jump box VM in a peered Virtual Network and Connect to it.
- Test the _Access_ to the Container App successfully from within the Jump Box VM.

## Create a Secured Container App restricting access within its Environment

In this Task, we'll build a Container App _Environment_ with access restricted to within this Environment only. This shows how easily a set of Container Apps can be put inside a secured boundary protecting it from outside world.

1. Run the following command to set the needed CLI variables. The variables set necessary values for cloud resources like a _Tenant_, _Resource Group_, _Location_, _Virtual Networks_ and _Azure Container Registry and its credentials_. The Virtual Network has to be supplied by User and will provide the **Secure Boundary** around Container Apps.

   ```bash
   tenantId="<tenantId>"
   subscriptionId="<subscriptionId>"
   resourceGroup="<resourceGroup>"
   location="<location>"
   monitoringResourceGroup="<monitoringResourceGroup>"
   logWorkspace="<logWorkspace>"
   securedEnvironment="secure-env"
   acrName="<acrName>"
   registryServer="<container_registry_server>"
   registryUserName="<container_registry_username>"
   registryPassword="<container_registry_password>"

   # Optional - NOT a requirement for Contyainer Apps but mostly for microservice applications
   storageName="<storage_account_name>"

   # Primary for Securing Container Apps
   containerAppVnetName="containerapp-workshop-vnet"
   containerAppVnetId=

   # Subnet for Control plane of the Container Apps Infrastructure
   controlPlaneSubnetName="containerapp-cp-subnet"
   controlPlaneSubnetId=
   ```

2. Run the following command to create a log analytics workspace. Container App will use this Log Analytics workspace to write its log information. Users can view this log from within Log section of the Container App menu in the portal.

   ```bash
   az monitor log-analytics workspace create --resource-group $monitoringResourceGroup --workspace-name $logWorkspace

   # Retrieve Log Analytics ResourceId
   logWorkspaceId=$(az monitor log-analytics workspace show --query customerId -g $monitoringResourceGroup -n $logWorkspace -o tsv)

   # Retrieve Log Analytics Secrets
   logWorkspaceSecret=$(az monitor log-analytics workspace get-shared-keys --query primarySharedKey -g $monitoringResourceGroup -n $logWorkspace -o tsv)
   ```

3. Run the following command to create a virtual network where the Container Apps will be injected. The Virtual Network will provide the Secured Boundary around Container Apps. Two Subnets need to be provided within this Virtual Network - **Control Plane** and **Apps Plane**. At this moment, both should be having **/21** address prefix.

   ```bash
   # Container App Vnet
   az network vnet create --name $containerVnetName --resource-group $resourceGroup --address-prefixes $containerVnetPrefix
   containerAppVnetId=$(az network vnet show --name $containerVnetName --resource-group $resourceGroup --query="id" -o tsv)

   # ControlPlane Subnet
   az network vnet subnet create --name $controlPlaneSubnetName --vnet-name $containerVnetName --resource-group $resourceGroup --address-prefixes $controlPlaneSubnetPrefix
   controlPlaneSubnetId=$(az network vnet subnet show -n $controlPlaneSubnetName --vnet-name $containerVnetName --resource-group $resourceGroup --query="id" -o tsv)

   # Apps Subnet
   az network vnet subnet create --name $appsSubnetName --vnet-name $containerVnetName --resource-group $resourceGroup --address-prefixes $appsSubnetPrefix
   appsSubnetId=$(az network vnet subnet show -n $appsSubnetName --vnet-name $containerVnetName --resource-group $resourceGroup --query="id" -o tsv)
   ```

4. Run the following command to create a secured Environment for Container App with maximum Security - restricting access from within the Environment; that is, only other Container Apps deployed within the Environment can access this. Note, this is by default whenever we're supplying a Virtual Network while creating the Environment as below.

   ```bash
   az containerapp env create --name $securedEnvironment --resource-group $resourceGroup \
   # Log Workspace Id and Secret
   --logs-workspace-id $logWorkspaceId --logs-workspace-key $logWorkspaceSecret --location $location \
   # Subnet for Control Plane Infrastructure
   --controlplane-subnet-resource-id $controlPlaneSubnetId \
   # Subnet for Container App(s)
   --app-subnet-resource-id $appsSubnetId
   ```

### Deploy Container App in Secured Environment

Let us deploy a simple Azure Function App, which responds to Http(s) calls and returns some formatted message as response with _version no. of the image is tagged to it_.

1. Create a [Storage account](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) on Azure.

> [!NOTE]
>
> Azure Function app needs a Storage account to cache its state and metadata.
>
> _This step can be skipped if already completed in the previous Task._

> [!TIP]
>
> Note down the **Connection String** of the Azure Storage account; this will be used later.

2. **Create** an Azure Function App project locally using Visual Studio Code.

> [!NOTE]
>
> Readers can create Azure Function App using any other preferred IDE as well.
>
> _This step can be skipped if already completed in the previous Task._

4. Select **Http trigger**.

5. Open Project workspace in Visual Studio Code

- Select **local.settings.json** file in the editor.

- Modify the **AzureWebJobsStorage** environment variable, replacing its value by the _Connection String_ value from previous step.

- Select the **httpcontainerapp.cs** file in the editor and update as below.

  > [!NOTE]
  >
  > Version no. is set as - **1.0.1**

  ```c#
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
              var response = $"Hello from Container {name}-v1.0.1";
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
     Hello from Container App-v1.0.1
     ```

### Create a secured Container app injected into the Virtual Network

Run the following command to create a Container App in the restricted, secured Environment where it can be accessed only from other container apps within the same Environment.

> [!NOTE]
>
> Although a Virtual Network is supplied to inject Container Apps into, access to the Container App is allowed only from within the Environment; that means _No Access even from Peered Virtual Networks_. This is the maximum security and ensures no InBound access at all from Public Internet.

> [!TIP]
>
> Think of secure, backend APIs that can only be accessed through a Front end Application or through some API Gateway. These type of applications, when deployed as Container Apps, are a good candidate for such restricted Environment - providing a complete secured boundary around APIs with negligible effort and cost!

```bash
az containerapp create --name httpcontainerapp-secured --resource-group $resourceGroup \
# Secured Environment for the Container App
  --image $httpImageName --environment $securedEnvironment \
  --registry-login-server $registryServer --registry-username $registryUserName \
  --registry-password $registryPassword \

  # Ingress: Internal; generates Private FQDN, no access from outside of the Virtual Network
  --ingress internal --target-port 80 --transport http \
  --min-replicas 1 --max-replicas 5 \
  --cpu 0.25 --memory 0.5Gi \
  --secrets azurewebjobsstorage=$azureWebJobsStorage \
  --environment-variables "AzureWebJobsStorage=secretref:azurewebjobsstorage"
```

- Application would run within a specified Virtual Network.

- Internal/Private FQDN for the form - _<APP_NAME>.internal.<UNIQUE_IDENTIFIER>.<REGION_NAME>.azurecontainerapps.io_.

- All Applications within the same _Secured Environment_ would share same **Internal/Private** IP address.

  ![containerapp-secure-env](../media/containerapp-secure-env.png "Container App - Environment")

  > [!NOTE]
  >
  > Please take a note of this FQDN as we will be using for testing the app later.

  > [!TIP]
  >
  > The FQDN is suffixed with the word **internal** depicting that the Ingress is not accessible publicly.

### Secure Environment

> [!NOTE]
>
> Please note the value for **Ingress Traffic** - _Limited to Container App Environment_.
> ![containerapp-secure-ingress](../media/containerapp-secure-ingress.png "Container App - Ingress restricted to Environment")

Details of the Container App Environment:

- **Virtual Network** - hosting the Container App and providing network security.
- **Private IP** - Static Private IP assigned to Container App from the associated Virtual Network.
- **No. of Apps** - Number of Apps hosted by the Environment.

![containerapp-env-overview](../media/containerapp-env-overview.png "Container App - Secured Environment")

### Apps hosted by Secure Environment

Multiple Applications can be hosted by same Container App Environment, within the same secured boundary.

![containerapp-env-apps](../media/containerapp-env-apps.png "Container App - Multiple Apps in same Environment")

### Access the Container App

A secured Container App is Not accessible from public Internet. The Environment used in the current Task is the most restrictive one and wouldn't allow any access from outside the Environment; not even from peered Virtual Network. Let us try to prove that by creating a peered Virtual network and deploying a Virtual Machine in it - called _Jump box VM_. Let su then try to access the above Container App from the Jim box VM and see the result.

Run the following commands to create a Virtual Network and a Subnet for hosting the _Jump box VM_ and then create peering between this Virtual Network and the one used for creating the Container App Environment.

```bash
# Jump Box Subnet
az network vnet subnet create --name $jboxSubnetName --vnet-name $jboxVnetName --resource-group $resourceGroup --address-prefixes $jboxSubnetPrefix
jboxSubnetId=$(az network vnet subnet show --name $jboxSubnetName --vnet-name $jboxVnetName --resource-group $resourceGroup --query="id" -o tsv)
# VNET peering between Container App Vnet and Jump Box VNet (In case two subnets are not within same Vnet)
az network vnet peering create --name $containerAppPeeringName --remote-vnet $jboxVnetId \
--resource-group $resourceGroup --vnet-name $containerVnetName --allow-vnet-access
az network vnet peering create --name $jboxPeeringName --remote-vnet $containerAppVnetId \
--resource-group $resourceGroup --vnet-name $jboxVnetName --allow-vnet-access
```

### Deploy Jump box VM

Execute the following steps to deploy the _Jump box VM_

1. Create a [Linux](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-portal) or [Windows](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal) virtual machine in the Azure portal. This will be our Jump box VM to access the Container App securely.

2. Deploy the VM in the above [Virtual Network](https://docs.microsoft.com/en-us/azure/virtual-network/ip-services/virtual-network-deploy-static-pip-arm-portal?context=/azure/virtual-machines/context/context).

3. Connect to the Jump box VM - [Linux](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows) or [Windows](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/connect-logon).

4. Access the Function App Url from within the Jump box - use _curl_, _wget_ or the _browser_.

   ```bash
   #Request
   https://<Private FQDN of the Container App>/api/container?name=App
   ```

5. Response should be something like below; proving that the app isn't accessible even from a _peered Virtual Network_.

   ![containerapp-secure-access](../media/containerapp-secure-access.png "Container App - No Public access")

### Remove the Container App and its Environment

We'll remove the Container App and Environment hosting it as we'll be creating a slightly relaxed yet secured Container App Environment and then host this App onto it.

## Create a Secured Container App restricting access within Virtual Networks

In this Task, we'll relax the restriction a bit from the previous Task and build a Container App **Environment** with access restricted to within Virtual Networks only. This will allow us to access the Container App from resources or services deployed in the same Virtual Network as the Container App Or from a peered Virtual Network.

1. Run the following command to set the needed CLI variables. The variables set necessary values for cloud resources like a _Tenant_, _Resource Group_, _Location_, _Virtual Networks_ and _Azure Container Registry and its credentials_. The Virtual Network has to be supplied by User and will provide the **Secure Boundary** around Container Apps.

   ```bash
   tenantId="<tenantId>"
   subscriptionId="<subscriptionId>"
   resourceGroup="<resourceGroup>"
   location="<location>"
   securedEnvironment="secure-env"
   acrName="<acrName>"
   registryServer="<container_registry_server>"
   registryUserName="<container_registry_username>"
   registryPassword="<container_registry_password>"

   # VNET for Securing Container Apps
   containerAppVnetName="containerapp-workshop-vnet"
   containerAppVnetId=
   containerVnetPrefix=""

   # Subnet for Control plane of the Container Apps Infrastructure
   controlPlaneSubnetName="containerapp-cp-subnet"
   controlPlaneSubnetId=
   controlPlaneSubnetPrefix=""

   # Private DNS zone for Container Apps
   containerAppLinkName="containerapp-dns-plink"

   # Subnet for hosting Container Apps
   appsSubnetName="containerapp-app-subnet"
   appsSubnetId=
   appsSubnetPrefix=""

   # VNET peering between Container App Vnet and Jump box VNet (In case two subnets are not within same Vnet)
   containerAppPeeringName="containerpp-jbox-peering"
   jboxPeeringName="jbox-containerpp-peering"
   ```

2. Run the following command to create a virtual network where the Container Apps will be injected. The Virtual Network will provide the Secured Boundary around Container Apps. Two Subnets need to be provided within this Virtual Network - **Control Plane** and **Apps Plane**. At this moment, both should be having **/21** address prefix.

   ```bash
   # Container App Vnet
   az network vnet create --name $containerVnetName --resource-group $resourceGroup --address-prefixes $containerVnetPrefix
   containerAppVnetId=$(az network vnet show --name $containerVnetName --resource-group $resourceGroup --query="id" -o tsv)

   # ControlPlane Subnet
   az network vnet subnet create --name $controlPlaneSubnetName --vnet-name $containerVnetName --resource-group $resourceGroup --address-prefixes $controlPlaneSubnetPrefix
   controlPlaneSubnetId=$(az network vnet subnet show -n $controlPlaneSubnetName --vnet-name $containerVnetName --resource-group $resourceGroup --query="id" -o tsv)

   # Apps Subnet
   az network vnet subnet create --name $appsSubnetName --vnet-name $containerVnetName --resource-group $resourceGroup --address-prefixes $appsSubnetPrefix
   appsSubnetId=$(az network vnet subnet show -n $appsSubnetName --vnet-name $containerVnetName --resource-group $resourceGroup --query="id" -o tsv)
   ```

3. Run the following command to create a secured Environment for Container App - restricting access from within the **Same** Virtual Network or **Peered** Virtual Networks. This is called - _Virtual Network Integration for Container Apps_. Follow this [excellent article](https://techcommunity.microsoft.com/t5/apps-on-azure-blog/azure-container-apps-virtual-network-integration/ba-p/3096932) to get a detailed view on this.

   ```bash
   az containerapp env create --name $securedEnvironment --resource-group $resourceGroup \
   --location $location \

   # Control Plane Subnet
   --controlplane-subnet-resource-id $controlPlaneSubnetId \

   # Application Subnet
   --app-subnet-resource-id $appsSubnetId --internal-only
   ```

   - **_--internal-only_** flag ensures that this environment can communicate with services on same virtual network or on a peered virtual network

   - Excluding **_--internal-only_** flag makes this environment reachable from other container apps in the same environment

     ![containerapp-ingress-pdns](../media/containerapp-ingress-vnetonly.png "Container App - Ingress restricted to VNet")

     > [!NOTE]
     >
     > Please note the value for **Ingress Traffic** - _Limited to VNet_.

### Configure the Secured Environment

The Secured Environment has been created with a Private IP chosen from the Virtual Network supplied during creation. Therefore services or resources deployed in the same Virtual Network or a peered Virtual Network should be able to access the Container App Url.

But to make it work, we need to make sure that the Private IP can be resolved within Azure through a Private DNS Zone and calling services or resources can then call the FQDN of the Private DNS Zone and access the Container App.

![containerapp-secure-env-ip](../media/containerapp-secure-env-ip.png "Container App - Configure Secured Environment")

### Create Private DNS Zone

Run the following commands to create a Private DNS Zone with the default Domain of the Secured Environment created earlier. The Private IP of the Environment needs to be resolved through this Private DNS Zone.

```bash
# Retrieve the default Domain name of the Secured Environment created earlier
defaultDomain=$(az containerapp env show --name $securedEnvironment --resource-group $resourceGroup --query="defaultDomain" -o tsv)
# Retrieve the static IP of the Secured Environment created earlier
staticIp=$(az containerapp env show --name $securedEnvironment --resource-group $resourceGroup --query="staticIp" -o tsv)
# Create the Private DNS Zone with default Domain
az network private-dns zone create --name $defaultDomain --resource-group $resourceGroup
# Show the details of Private DNS Zone with default Domain
az network private-dns zone show --name $defaultDomain --resource-group $resourceGroup
```

### Create wild card _A record_

We need to add this Private IP to the Private DNS Zone as an A-record.

```bash
# Create an A-record in the Private DNS Zone with the Private IP of the Environment created earlier
az network private-dns record-set a create --name "*" --resource-group $resourceGroup --zone-name $defaultDomain
az network private-dns record-set a add-record --ipv4-address $staticIp --record-set-name "*" \
--resource-group $resourceGroup --zone-name $defaultDomain
```

![containerapp-pdns](../media/containerapp-pdns.png "Container App - Overview of Private DNS Zone")

![containerapp-pdns-a-record](../media/containerapp-pdns-a-record.png "Container App - Create A-record")

### Link Virtual Networks

Any Virtual Networks that need to access the Container App should be linked here.

> [!NOTE]
>
> Even if the Virtual Networks are peered, for resolving the Private IP through this Private DNS Zone, each intended Virtual Network need to be added as a link in the Private DNS Zone.
> Run the following commands to create Virtual Network links:

- Virtual Network of Container App.
- Virtual Network of Jump Box VM.

```bash
az network private-dns link vnet create --name $containerAppLinkName --resource-group $resourceGroup \
--virtual-network $containerAppVnetName --zone-name $defaultDomain
#az network private-dns link vnet show --name $containerAppLinkName --resource-group $resourceGroup --zone-name $defaultDomain
az network private-dns link vnet create --name $apimLinkName --resource-group $resourceGroup \
--virtual-network $apimVnetName --zone-name $defaultDomain
#az network private-dns link vnet show --name $apimLinkName --resource-group $resourceGroup --zone-name $defaultDomain
```

![containerapp-vnet-plink](../media/containerapp-vnet-plink.png "Container App - VNet links in Private DNS Zone")

## Access the Container App Securely

We can follow the steps as described in [Deploy Jump box VM](#Deploy Jump box VM)

1. Open the corresponding terminal on the VM.

   ```bash
   PS C:\Users\master-js-winvm> nslookup <Private FQDN of the Container App>
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    <The Private FQDN of the Container App>
   #Private IP address of the Container App environment
   Address:  27.0.0.4
   ```

2. Access the Function App Url from within the Jump box - use _curl_, _wget_ or the _browser_.

   ```bash
   #Request
   https://<Private FQDN of the Container App>/api/container?name=App

   #Response
   Hello from Container App-v1.0.1
   ```

## Conclusion

Congratulations! You've just now successfully secured your first Container app on Azure.
