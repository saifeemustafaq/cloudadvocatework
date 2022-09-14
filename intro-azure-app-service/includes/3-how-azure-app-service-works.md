Let’s explore how Azure App Service utilizes HTTP-based service for hosting web applications, REST APIs, and mobile back ends.

Azure app service runs and scales on Windows and Linux-based environments, and you can develop your application in any runtime such as like, .NET, .NET Core, Java, Ruby, Node.js, PHP, or Python.

## App service features

Azure App Service consists of several features that provide your application engineers with a complete web and API deployment platform.

In this unit, you'll learn about the following Azure App Service features:

- **Scaling**: You can scale your apps to service millions of users.
- **Authentication and Authorization**: App Service can use built-in auth and federated identity, with the latter supporting providers like Azure AD, Facebook, Twitter and so on.
- **Networking**: You can deploy apps that are accessible directly via the Internet, but you can also deploy in such a way that you can control inbound outbound traffic.

### App Service Scaling

A huge benefit to using a platform like Azure App Service is that scaling is something that just works. Before, on premise, means you might resort to planning for holidays and sales or other events where you might need more servers to meet the increase in traffic.

With App Service, and being in the cloud, it scales up and out, to meet an increase in traffic. It also scales down when needed. Here’s what the scaling concepts mean:

- **Scale up** means you get more CPU, memory, disk space, and extra features like dedicated virtual machines (VMs), custom domains and certificates, staging slots, autoscaling, and more.
- **Scale out** means it increases the number of VM instances that run your app. You can scale out to as many as 30 instances, depending on your pricing tier.

So, what types of scaling is interesting for your business scenarios:
Scaling features:

- **Autoscaling**: Azure App service automatically sense when it’s time to scale up or out or even down. If you have a big holiday for your e-commerce app, having auto scaling means you don’t have to worry about those extra customers visiting your site.
- **Metric based scaling**: With Azure App Service, you can set up rules to configure the scaling. You can, for example, increase the number of instances by 1 if CPU load hits 70%.
- **Schedule based scaling**: You can also configure scaling so that it scales on specific date and time intervals, which is beneficial if you expect higher application usage, such as a seasonal sale on your e-commerce website.
- **Per-app scaling**: Normally, your apps scale based on the app service plan they are on. You can also enable per-app scaling, meaning the app can be set to scale independently from the app service plan. So, you could have the app service plan set to scale to 10 instances, but the app set to five instances. This scaling choice might make sense if you have many apps running on the same app service plan and you want more fine-grained control.

### App Service Authentication and Authorization

The authentication and authorization module runs in the same sandbox as the application code. In Linux and containers, the authentication and authorization module run in a separate container, isolated from the application code.

When it's enabled, every incoming HTTP request passes through it before being handled by the application code. This module handles several things for the app:

- Authenticates users with the specified provider.
- Validates, stores, and refreshes tokens.
- Manages the authenticated session.
- Injects identity information into request headers.
  The module runs separately from the application code and configures using app settings. No SDKs, specific languages, or changes to the application code are required.

## App Service Networking

There are two main deployment types available for App service:

- **multi-tenant public service**: Each scale unit contains many App Service plans.
- **single-tenant App Service Environment (ASE)**: Hosts Isolated SKU App Service plans directly in your Azure virtual network. The ASE plan is the scale unit.

What network features you use will depend on the chosen deployment type.

### Multi-tenant public service

Requests from HTTP or HTTPS are handled by roles called front ends. Then there are other roles that hosts customer workload known as workers. Because there can be many customers on the same App service scale unit, you can’t connect it directly to your network.

Therefore, you abstract away the networking connection bit and instead talk about features, features to handle app communication. You also think of a feature as being inbound or outbound and what features can be applied where:

| Inbound features     | Outbound features                            |
| -------------------- | -------------------------------------------- |
| App-assigned address | Hybrid Connections                           |
| Access restrictions  | Gateway-required virtual network integration |
| Service endpoints    | Virtual network integration                  |
| Private endpoints    |                                              |
