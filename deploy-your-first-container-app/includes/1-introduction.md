[Azure Container Apps(Preview)](https://docs.microsoft.com/azure/container-apps/overview) enables users to run containerized applications in a completely Serverless manner providing complete isolation of _Orchestration_ and _Infrastructure_. Applications built on Azure Container Apps can dynamically scale based on the various triggers as well as [KEDA-supported scalers](https://keda.sh/docs/scalers/).

Features of Azure Container Apps include:

- Run multiple **Revisions** of containerized applications. By supporting multiple revisions, application is more fault tolerant and is able to return back to a previous revision, in case the current revision fails.
- Easily implement **Blue/Green** deployment and perform **A/B Testing** by splitting traffic across multiple versions of an application.
- **Autoscaling** apps based on variety of scale triggers. This helps distributing the load across multiple instances of the same application revision ensuring high availability and responsiveness.
- Enable HTTPS **Ingress** without having to manage other Azure infrastructure like _L7 Load Balancers_.
- Manage Application **Secrets** securely.
- **Secure** access to Container Apps. This can be achieved in two ways
  - Use [Virtual Network Integration](https://techcommunity.microsoft.com/t5/apps-on-azure-blog/azure-container-apps-virtual-network-integration/ba-p/3096932) feature to securely access Container Apps from same or a peered virtual Network.
  - Use [Secure Environment](https://docs.microsoft.com/azure/container-apps/vnet-custom?tabs=bash&pivots=azure-cli) feature to restrict access to Containers Apps from within the environment (_Not even from within a Virtual Network_!) ensuring complete isolation.
- View **Application Logs** using _Azure Log Analytics_.
- **Azure CLI** extension or **ARM** templates to automate management of containerized applications.

## Scenario

![azure-container-apps-example-scenarios](/Users/monojitdattams/Development/Projects/Training_Projects/Learn-Pr/learn-pr/learn-pr/azure/deploy-your-first-container-app/media/azure-container-apps-example-scenarios.png)

Azure Container Apps enables users to run containerized applications in a completely Serverless manner providing complete isolation of _Orchestration_ and _Infrastructure_. A few common uses of Azure Container Apps include:

- Deploying **API endpoints** - Applications that expose an Http(s) endpoint.
- Hosting **Background processing** applications - Long running background Jobs.
- Handling **Event-driven processing** - Applications that only react to an event for example. A message in the queue or an image in the Blob.
- Running **Micro-services** - Independent applications, can be a combination of all the above 3 types and can communicate with each other.

## What will you learn?

In this module, you will:

- Understand the Concepts of Container Apps and when to use it.
- Various ways of Deploying Container Apps on Azure.
- How to Secure Container Apps.
- Understand and Implement the Advanced Concepts of Container Apps - _Scaling, Traffic Splitting, Observability_.

## What is the main objective

You will learn to deploy your first containerized app using Azure Container Apps.
