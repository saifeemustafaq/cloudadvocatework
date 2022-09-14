In this unit, you learn about how Azure Container Apps works along with some foundational concepts.

## Container management

Containers bundle together an application’s code with required configuration files, libraries, and with the dependencies.

- Containers share the host machine's operating system (OS) kernel, removing the need for a whole OS instance for each program and keeping container files small.

- Azure Container Apps manages the details of container orchestrations by giving you control over deployment and scale rules for your applications.

- Containers in Azure Container Apps can use any runtime, programming language, or development stack of your choice.

- Individual container apps are deployed to a single Container Apps environment, which acts as a secure boundary around groups of container apps. Container Apps in the same environment are deployed in the same virtual network and write logs to the same Log Analytics workspace.

## Revision

Azure Container Apps implements container app versioning by creating revisions. A revision is an immutable snapshot of a container app version. The first revision is automatically created when you deploy your container app.

Revisions are managed in the following ways:

- New revisions are automatically created when a container app's template configuration changes.
- While revisions are immutable, they're affected by changes to global configuration values, which apply to all revisions.

- The following create a new revision:

  - Changes to containers
  - Changes to scaling rules
  - Changes to Dapr settings
  - Any change that affects the template section of the configuration

- The following types of changes do not create a new revision:
  - Changes to traffic splitting rules
  - Turning ingress on or off
  - Changes to secret values
  - Any change outside the template section of the configuration

Once a revision is no longer needed, you can deactivate individual revisions, or choose to automatically deactivate old revisions.

You can also use revisions to manage traffic direction strategies include A/B testing and BlueGreen deployment.

## Application lifecycle management

The Azure Container Apps application lifecycle revolves around revisions.
A container app flows through three phases: Deployment —> Update —> Deactivate

1. **Deployment**
   As a container app is deployed, the first revision is automatically created.
   :::image type="content" source="../media/deployment.png" alt-text="Deployment phase of the application lifecycle.":::

2. **Update**
   As the container app is updated, a new (and active) version of the existing revision is created.
   :::image type="content" source="../media/update.png" alt-text="Update phase of the application lifecycle.":::
   Now that the new revision is created, you can choose whether to deactivate previous versions automatically or to keep them available for traffic control.

3. **Deactivate**
   Once a revision is no longer needed, you can deactivate a revision with the option to reactivate later.
   :::image type="content" source="../media/deactivate.png" alt-text="Deactivation phase of the application lifecycle.":::
   During deactivation, the container is shut down.

   Other reasons a container may shut down include:

   - As a container app scales in
   - As a container app is being deleted
   - As a revision is being deactivated

## Scaling your applications

Applications built on Azure Container Apps can dynamically scale based on the following characteristics:

- **HTTP traffic**: HTTP requests may be divided between multiple revisions. For example, the image below depicts the first revision receiving 80% of the traffic and the second (new revision) receiving 20%.

  > [!NOTE]
  > Scaling Criteria: In HTTP traffic, scaling is determined by the number of simultaneous HTTP request.

  :::image type="content" source="../media/traffic-split.png" alt-text="HTTP Traffic splitting between two Revisions":::
  With an HTTP scaling rule, you have control over the threshold that determines when to scale out.

  The container app scales out to multiple replicas and can scale in to zero instances according scaling threshold set by you.

- **Background Processing**: CPU and memory scaling allows your app to scale in or out depending on how much the CPU memory is being used. This type of scaling doesn't allow your container app to scale to zero.

  > [!NOTE]
  > Scaling Criteria: Scaling is determined by the level of CPU or memory load.

  :::image type="content" source="../media/BackgroundProcessing.svg" alt-text="Image depicting additional CPUs added as an app scales out.":::

  The container app scales when CPU or memory usage exceeds the threshold specified in the metadata. You may also specify the minimum and maximum replicas that remain in the memory for apps that scale based on utilization.

- **Event-driven processing**: Container Apps can scale based of a wide variety of event types. Any event supported by KEDA is supported in Container Apps.

  > [!NOTE]
  > Scaling Criteria: Scaling is determined by event load.

  :::image type="content" source="../media/EventDrivenScaling.svg" alt-text="Image showing an queue message server.":::

  Each event type features different properties in the metadata section of the KEDA definition. Using various properties to define a scale rule in Container Apps.

  For example, new replicas are created as the messages count in a message queue exceeds the limit specified in the metadata.

- **Microservices**: Deploy and manage a microservice architecture with the option to integrate with Dapr. Dapr (Distributed Application Runtime) is an open-source project sponsored by Microsoft. Dapr is a portable, serverless, event-driven runtime that makes it easy for developers to build microservices that run on the cloud and edge.

  > [!NOTE]
  > Scaling Criteria: Individual microservices can scale according to any KEDA scale trigger.

  :::image type="content" source="../media/Microserves.svg" alt-text="Image depicting a series of interconnected microservices.":::

  Dapr helps you leverage the benefits of a sidecar architecture and tackle the challenges that come with building microservices.
