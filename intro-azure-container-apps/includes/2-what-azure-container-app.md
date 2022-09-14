Serverless computing and containerized applications are often some of the most efficient ways to deploy, test, manage, and maintain your application. In this module, you'll learn how application development has evolved, and how Azure Container Apps fits into the modern application development lifecycle.

In this module, we'll understand how Azure Container Apps helps you build your containerized serverless application, and easily develop and manage your microservices.

To begin, get familiar with a few terms as you get acquainted with Azure Container Apps:

- **Azure** : Microsoft Azure is a global platform that enables users to engage in cloud computing and is designed for creating and managing apps through Microsoft's data centers.

- **Container** : A container packages together a complete runtime environment including:

  - An application
  - Application dependencies
  - Libraries and binaries
  - Configuration files

- **Deployment** : After you've built and developed your application, you push it to a production server or a serverless platform to make it available online and accessible to end users.

  - You deploy a container on cloud platforms such as Microsoft Azure Container Instances (ACI) or Azure container Apps (ACA).

- **Scale** : Your application must meet demand as requests for processing rise and fall. Scaling refers to the automatic addition and removal of computational resources based on demand.

  - Your application scales out on-demand to service large number of requests. Scaling out prevents your application from crashing from an overload of requests.
  - Applications scale in when there are few or no requests to the application. Scaling in optimizes the allotment of resources to your application

- **Container orchestrations** : Container orchestration is automatic container management including:
  - Container scheduling
  - Deployment
  - Networking
  - Scaling
  - Health monitoring

## What is serverless computing?

Serverless computing allows you to run applications without worrying about the underlying infrastructure or physical servers. This type of flexibility allows applications to scale out and in based on demand for the application.

Developers often migrate their on-premise applications or existing cloud-hosted applications to a serverless platform and host apps on machines maintained by Microsoft.

:::image type="content" source="../media/ServerlessStructure1.png" alt-text="Image depicting serverless architecture":::

## What are microservices?

A single application that contains various services and features is known as monolithic application. By contrast, microservices split application features into smaller services that run independently.

Using a microservices architecture allows your application to benefit from the following features:

- **Highly maintainable and testable**: Building a small application to address a single business concern often makes the codebase easier to understand and maintain.
- **Loosely coupled**: Building microservices allows you to minimize dependencies between services.
- **Independently deployable**: Microservices are independent features of your application where deployment is possible independent of other services.
- **Owned by dedicated teams**. The microservices architecture allows different teams to maintain different services.

With microservices approach your application becomes highly fault tolerant, even if one of the service stops responding, the other aspects of the application may not be impacted by the downtime.

In the event that a service stops responding, you have a known place to look for bugs and troubleshoot the problem.

A microservices architecture allows developers to work on services without concern of adversely affecting the application as a whole.

:::image type="content" source="../media/Monovsmicro.svg" alt-text="Azure Container Apps Logo":::

## Azure Container Apps

A container is a software package that includes the code and all dependencies to run an application on any computing environment.

Azure Container Apps is container orchestration platform that enables you to run microservices and containerized applications on a serverless platform. With Container Apps, you enjoy the benefits of running containers while avoiding the hassles of manually configuring cloud infrastructure and complex container orchestrators.

Azure Container Apps adds a layer of abstraction on top of leading container orchestration platforms which simplifies the process of running containers. Container Apps provides first-class support for [Dapr](https://dapr.io/) (Distributed Application Runtime) for service discovery, state management and secret management and [KEDA](https://keda.sh/) (Kubernetes Event-Driven Autoscaling).
