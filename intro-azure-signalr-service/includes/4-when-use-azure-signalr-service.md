## When to use Azure SignalR Service

Here, we'll consider whether SignalR service is the correct option to implement while developing your website that will have real-time communication feature. We'll analyze criteria to help you decide, including:

- Scalability.
- Support for multiple clients.
- Communication latency.

> [!TIP]
> Azure SignalR service has a free tier that you can use to get started at no cost.

### Decision criteria

Azure SignalR Service is primarily used by applications that require push notifications from server to client; for example, applications such as auctioning apps, chat, stock market, gaming, and dashboards.

While developing any of these applications, you should ensure that real-time communication is implemented between the client and server components.

Here are some factors that will help you decide whether Azure SignalR Service is the best option for you.

| Criteria                     | Analysis                                                                                                                                                                                                                                     |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Scalability                  | It supports SQL Server, Redis, Azure Service Bus, and more to achieve scalability.                                                                                                                                                           |
| Communication latency        | It's used to create low-latency communication channels between applications.                                                                                                                                                                 |
| Support for multiple clients | It supports multiple clients ranging from: JavaScript clients for current browsers like Chrome, Firefox, Safari, Microsoft Edge, and Internet Explorer 11. .NET clients including Xamarin for Android and iOS mobile apps. Java 8 and later. |
