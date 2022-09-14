In this unit, we'll describe the major components of the service and the architecture patterns at your disposal.

## Internals

Azure SignalR Service is built on top of ASP.NET Core SignalR framework. It also supports ASP.NET SignalR by reimplementing ASP.NET SignalR's data protocol on top of the ASP.NET Core framework.
:::image type="content" source="../media/Internals.png" alt-text="Diagram showcasing Internals of Azure SignalR":::

## Hubs

The Azure SignalR Service is essentially a logical transport layer between application server and clients. All persistent connections are offloaded to SignalR Service. Application server only needs to handle the business logic in hub class, without worrying about client connections.

SignalR Service doesn't save or store customer data, all customer data received is transmitted to target server or clients in real-time.

When a client is connected to the SignalR Service, service runtime will find a server connection to serve this client

- This step happens only once, and is a one-to-one mapping between the client and server connections.

- The mapping is maintained in SignalR Service until the client or server disconnects.

At this point, the application server receives an event with information from the new client. A logical connection to the client is created in the application server. The data channel is established from client to application server, via SignalR Service.

SignalR Service transmits data from the client to the pairing application server. And data from the application server will be sent to the mapped clients.

## Architectural approaches

There are three different approaches to adding Azure SignalR Service to your app:

- **ASP .NET app**, here you add NuGet package, the ASP.NET Core library, provision an Azure SignalR Service but you end up hosting the app yourself on App Service.

- **Azure Function app**. Here, you build the backend part of your app as a Function App. You essentially define routes to handshake between a calling client and your backend and you also provide routes capable of receiving and sending messages to clients.

- **REST API**. Here you call specific endpoints. You can, for example, build a console app to interact with Azure SignalR Service.

## Why a managed service?

One of the key reasons to use the Azure SignalR Service is simplicity. With Azure SignalR Service, you don't need to handle problems like performance, scalability, availability, and disaster recovery. These issues are handled for you with a 99.9% service-level agreement.

- **Scaling**: Common solutions leverage: DNS load balancing, hardware load balancers, and software load balancing. Azure SignalR Service handles this scaling problem for you. You can autoscale single instance and multiple instances.

- **Disaster Recovery**: Azure SignalR Service already guarantees 99.9% availability, but it's still a regional service. Your service instance is always running in one region and won't fail over to another region when there's a region-wide outage.
  :::image type="content" source="../media/SignaR_Disaster_Recovery.png" alt-text="Diagram showcasing SignalR managing disaster recovery":::

- **Security**: Azure SignalR Service, allows you to create private endpoints to secure the traffic between resources in your virtual network and Azure SignalR Service. You can even assign the built-ins for a security control individually to help make your Azure resources compliant with the specific standard.

- **Azure Active Directory**: Azure SignalR Service supports using Azure Active Directory (Azure AD) to authorize requests to SignalR resources. With Azure AD, you can use role-based access control (RBAC) to grant permissions to a security principal.
