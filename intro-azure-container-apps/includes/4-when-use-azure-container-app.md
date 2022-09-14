In this unit, you learn when should you use Azure Container Apps and review scenarios where Azure Container Apps can boost your DevOps process.

## Containers

A container is a bundled package of a runtime environment which includes an application with all its required dependencies and configuration files.

Azure Container Apps provides mechanisms for deploying, maintaining and scaling your containerized applications.

### Supported technologies

- Any Linux-based x86-64 (Linux/amd64) container image
- Containers from any public or private container registry, including:
  - Docker Hub Container Registry
  - Azure Container Registry (ACR)
  - GitHub Package Registry

## Environment

As you create an Azure Container Apps environment, a virtual network (VNET) is created for you, or you can provide your own virtual network.

You should deploy your container apps in a single environment when you wish to:

- Manage related services
- Deploy different applications to the same virtual network
- Have applications communicate with each other using Dapr
- Have applications to share the same Dapr configuration
- Have applications share the same log analytics workspace

Alternatively, you can deploy your container app in different environments, so that:

- Two applications never share the same compute resources
- Two applications can't communicate with each other via Dapr

  :::image type="content" source="../media/environments.png" alt-text="Features":::
  Containers in Azure Container Apps can use any runtime, programming language, or development stack of your choice.

## What's more?

Azure offers a wide selection of container-related services.

- **Azure Container Instances (ACI)** gives you control over a single isolated container.
- **Azure Kubernetes Service (AKS)** manages your hosted Kubernetes environment and simplifies deployment and management of containerized applications in Azure.
- **Web App for Containers (App Services)** used for public facing websites and APIs.
  However, Azure Container Apps sits above all as abstraction for all of these services.

  :::image type="content" source="../media/ACA with ACI and AKS.png" alt-text="ACA above ACI and AKS":::

  Azure Container Apps also allows you to run front-end web apps, enable a microservice architecture, and manage background processing tasks for your application.

## Features

The following list summarizes some commonly-used Container Apps features.

- **Run multiple container revisions** and manage the container app's application lifecycle.
- **Run containers from any registry**, public or private, including Docker Hub and Azure Container Registry (ACR).
- **Use the Azure CLI extension or ARM templates** to manage your applications.
- **View application logs** using Azure Log Analytics.
- **Autoscale** your apps based on any KEDA-supported (Kubernetes Event-driven Autoscaling) scale trigger. Most applications can scale to zero.
- **Split traffic** across multiple versions of an application for Blue/Green deployments and A/B testing scenarios.
- **Enable HTTPS ingress** without having to manage other Azure infrastructure.
- **Use internal ingress and service discovery** for secure internal-only endpoints with built-in DNS-based service discovery.
- **Build microservices with Dapr** and access its rich set of APIs.
- **Provide an existing virtual network** when creating an environment for your container apps.
- **Securely manage secrets** Azure Container Apps allows your application to securely store sensitive configuration values. Once the values are defined at the application level, secured values are available to containers, inside scale rules, and via Dapr.
