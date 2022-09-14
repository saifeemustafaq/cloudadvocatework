In this unit, we'll cover security concepts of Azure Container apps. These concepts will allow us to build a secured boundary around Container Apps and restrict access from the outside world.

## Overview

![azure-container-apps-environments](../media/azure-container-apps-environments.png "Container App - Environment")

While creating a Container Apps [Environment](https://docs.microsoft.com/en-us/azure/container-apps/environment), a virtual network (VNET) is automatically created. But users can provide their own Virtual Network as well. Network addresses are assigned from a subnet range is defined as the environment is created.

- Subnet range used by the Container Apps environment is controlled by the user.
- Once the environment is created, the subnet range is immutable.
- A single load balancer and single Kubernetes service are associated with each container apps environment.
- Each [Revision Pod](https://docs.microsoft.com/en-us/azure/container-apps/revisions) is assigned an IP address from within the subnet.

## Ingress

Ingress exposes Container App to the public web and eliminates need to create an _Azure Load Balancer_, _Public IP address_ or any other Azure resources to enable incoming HTTPS request. Primary characteristics of Ingress are:

- Supports **TLS** termination.
- Supports HTTP/1.1 and HTTP/2.
- Supports **WebSocket** and **gRPC**.
- HTTPS endpoints always use **TLS 1.2**, terminated at the ingress point.
- Endpoints always expose ports 80 (_for HTTP_) and 443 (_for HTTPS_). By default, HTTP requests to port 80 are automatically redirected to HTTPS on 443.
- Request timeout is **240 seconds**.

Configuration of Ingress is an Application-wide setting and changes in the Ingress settings apply to all **Revisions** simultaneously, and no new **Revisions** are generated. Application is assigned a **Fully qualified domain name (FQDN)** when Ingress is enabled, Please refer to [IP addresses and domain names](https://docs.microsoft.com/azure/container-apps/ingress?tabs=bash#ip-addresses-and-domain-names) for details on how Https Ingress can be configured for Container Apps. Following are some of the important features of Ingress:

- Container Apps environment has a single public IP address for applications with **External** ingress visibility, and a single internal IP address for applications with **Internal** ingress visibility.
- All applications within a Container Apps environment with **External** ingress visibility share a single **Public IP address**.
- All applications within a Container Apps environment with **Internal** ingress visibility share a single **Internal/Private IP address**.
- InBound access to the Container Apps Environment can be restricted as following:

  - **From only within the Environment** - this is maximum security and is the default behaviour when we integrate Container App with a Virtual Network.
  - **From within a Virtual Network** - This can be configured while creating the Environment and restricts access from within the same Virtual Network as the Container App itself or from peered Virtual Network.

- **HTTP traffic** is routed to individual applications based on the **FQDN** in the _host header_.

## Azure Private DNS zone

Azure Private DNS provides a reliable, secure DNS service to manage and resolve domain names in a virtual network without the need to add a custom DNS solution. By using private DNS zones, you can use your own custom domain names rather than the Azure-provided names available today. DNS resolution against a private DNS zone does not work from public internet but only from virtual networks that are linked to it. A Private DNS zone can be linked to one or more virtual networks by creating [virtual network links](https://docs.microsoft.com/en-us/azure/dns/private-dns-virtual-network-links).

We will use a private DNS zone, supplied by user, to resolve the **Private IP** of the _Secured Container App Environment_, that will be created in the final task of the next exercise. This way services or resources intend to access the Container App securely can call the FQDN of the Private DNS zone from the same Virtual Network as the Container App itself or from peered Virtual Network. This ensures no public access from outside at all!
