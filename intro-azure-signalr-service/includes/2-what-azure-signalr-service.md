In this unit, we'll attempt to explain what the Azure Signalr Service is and it’s connected concepts.

Let’s start by explaining some concepts high-level first and do a deeper dive later in the unit.

### Concepts

There are three major concepts we'll cover:

- **SignalR**: It's an API for server-to-client remote procedure calls (RPC).
- **ASP .NET Core SignalR**: This is a .NET library. It’s possible to use SignalR and self-host, but you can also use the Azure SignalR Service with all the scaling and other benefits that comes with it.
- **Azure SignalR Service**: Provides the backplane, server instance and other things that come with the Azure platform.

### Azure SignalR Service

Azure SignalR Service is an Azure service you can use to provide real-time capability to your app. It enables you to push content to listening clients. What you get is clients being updated without the need to poll the server for the latest information.

### Several transports

SignalR is an API that sits on top of some concepts on what the user perceives as things happening in real-time.

Depending on your browser capability, SignalR will firstly attempt to use WebSockets. If you lack the browser support, it will fall back to use polling. But what are there concepts:

- **Long polling**: When a client app polls the server, the server doesn't respond to the query until there's new data or the connection timeout limit is reached. This greatly reduces the processing and network load on the server compared to short polling. The server maintains the connection for as long as possible and the client waits for a response or timeout before polling again.
- **WebSockets**: is an industry-wide standard to create an asynchronous, bi-directional, full-duplex communication channel over the Internet. WebSockets makes it possible to open an interactive communication session between server and client. Either side can initiate communication, eliminating the need for polling except as a fallback method.
