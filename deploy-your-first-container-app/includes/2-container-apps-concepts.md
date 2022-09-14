In this unit, we'll cover application concepts of Azure Container apps. These are central concepts that will help you use the Azure Container apps service.

## Overview

![azure-container-apps-revisions](../media/azure-container-apps-revisions.png "Container App - Overview")

Being able to scale our apps is an important thing to meet the demands of the business. You might encounter increase in traffic due to holiday sales for example. Applications built on Azure Container Apps can dynamically scale based on the following characteristics:

- **HTTP traffic** - when there's an increase in HTTP traffic (that is, _number of Concurrent requests_), Azure Container Apps can scale up and down as needed.
- **Event-driven processing** - Azure Container Apps can scale based on some external events, for example, when Messages in a Queue or Images in a Blob exceeds defined threshold; and can also scale down as the items are processed and moved out of the Queue or Blob.
- Any [KEDA-supported scaler](https://keda.sh/docs/scalers/) - Azure Container Apps supports all KEDA scalers, thus allowing applications to react and Scale up/down based on various external events for example, Azure services like Service Bus, Event Hubs or even third party services like RabbitMQ, Redis Streams etc.
- **CPU** or **Memory** load - when Average CPU or Memory usage across all instances of an application exceed some defined value, Azure Container App can scale the underlying applications accordingly and again scale it down when the load subsides.

## Environment

![azure-container-apps-environments](../media/azure-container-apps-environments.png "Container App - Environment")

**Environment** provides an isolation boundary around a collection of Container Apps. Individual container apps can be deployed to a single environment; multiple Container Apps can co-exist in the same Environment. Container Apps Environment can be secured by providing an [existing virtual network](https://docs.microsoft.com/azure/container-apps/vnet-custom) while creating it and then restricting the access of the apps within the Environment (_maximum security_) or within the Virtual Network.

Deploy Container Apps to the **Same** Environment in the following scenarios:

- Manage related services.
- Deploy different applications to the same virtual network.
- Applications need to communicate with each other.
- Applications share the same log analytics workspace.

Deploy Container Apps to the **Different** Environment in the following scenarios:

- Applications Don't share the same compute resources.

- Applications Don't need to communicate with each other.

## Containers

![azure-container-apps-containers](../media/azure-container-apps-containers.png "Container App - Containers")

Azure Container Apps has the following features with respect to the underlying Containers and its corresponding images:

- It supports any _Linux-based x86-64 (linux/amd64)_ Container image and from any public or private container registry.
- No _Base container image_ is required.
- Automatic _Restart_ of the Container App if there's any _Crash_ in underlying Container.
- Easy deployment options for multiple _Revisions_ using Azure CLI or ARM template.

## Revisions

![azure-container-apps-revisions](../media/azure-container-apps-revisions.png "Container App - Revisions")

Revisions are **Immutable snapshot** of a Container App. First Revision is automatically created when the Container App is initially deployed. New Revisions are created when the configuration of a Container App changes. By default all traffic is sent to the **Latest Revision** unless otherwise configured. Revisions have the following features:

- Can be configured to be **Active** or **InActive**.
- No charges for **Inactive** Revisions.
- Traffic is only sent to **Active** Revisions and suspended for **Inactive** Revisions.
- Global configuration values apply to all Revisions.

## Secrets

Secrets provide mechanism to securely store sensitive values specific to Applications. These values can be any secret values that are used as Environment variables within the Containers. Secrets are scoped to an application, outside of any specific **Revision** of the application. Each Application **Revision** can reference one or more Secrets and Multiple **Revisions** can reference the same Secret(s). Secrets in Container Apps have following features:

- _Adding, Removing_ or _Changing_ secrets doesn't generate new **Revisions**.
- A new _Revision_ need to be **Deployed** **_Before_** a _Secret_ is **Deleted**.
- A _Revision_ need to be **Re-started** when a _Secret_ is **Updated**.
- **Application Secrets** are referenced via the **secretref** property.
  - _Secret_ values are mapped to _application-level secrets_ where the **secretref** value matches the secret name declared at the application level.

## Container Registry

Container Apps need to fetch images from a Container registry and then create the container for us. This Container Registry can be any Private or Public registry and can be passed to the Container App while creating it. Although it's always recommended to point to a Private registry like Azure Container Registry where scanned images can be stored and thus providing security to the underlying containers of the Container App. For more information, see [Azure Container Registry documentation](https://docs.microsoft.com/en-us/azure/container-registry/#:~:text=Azure%20Container%20Registry%20allows%20you,container%20development%20and%20deployment%20pipelines.).

In the current module, we'll use an Azure Container Registry with Admin access enabled - which will provide the following three values to us for use with Container Apps:

- Registry Server **Hostname**.
- The **Username** to log in to container registry.
- The **Password** to log in to container registry.

### Azure CLI Extension for Container Apps

Following are the two resources needed for deploying and managing Container Apps through Azure CLI:

- **Azure Container Apps Extension** for Azure CLI.
- **Microsoft.App** namespace.
