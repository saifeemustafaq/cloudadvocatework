In this Module, we've learned

> [!div class="checklist"]
>
> - What is Container App and when to use it?
> - Understand Basic and Advanced Concepts of Container Apps
> - Create and Deploy a Container App on Azure
>   - Http triggered Azure Function App
> - Secure access to Container Apps
>   - Internal Ingress
>   - VNET integration
> - Implement various fractures of Container Apps
>   - Scale
>   - Split Traffic
>   - Manage Revisions
>   - Automatic Deployment using ARM template
>   - Monitoring and Logging
> - Connect to Container Apps securely using a Jump box VM and Test end to end

## Further enhancements

- Create non-Http triggered Azure Functions and Test the Container App flow (_non-Ingress scenario_). For an example, see [Deploy a background processing application with Azure Container](https://docs.microsoft.com/en-us/azure/container-apps/background-processing?tabs=bash).
- Create _Connected Microservices_ using Azure API Management as the gateway and having various container apps as backend APIs. For an example, see [Deploy a Dapr application to Azure Container Apps](https://docs.microsoft.com/en-us/azure/container-apps/microservices-dapr?tabs=bash)
- Extend _Connected Microservices_ scenario by brining Azure Application Gateway and WAF into the architecture. For concepts and how to implement, see [Azure Application Gateway features](https://docs.microsoft.com/en-us/azure/application-gateway/features) and [How Application Gateway works](https://docs.microsoft.com/en-us/azure/application-gateway/how-application-gateway-works)

## Learn More

- [Comparing Container Apps with other Azure container options](https://docs.microsoft.com/en-us/azure/container-apps/compare-options)
- [Microservices with Azure Containers Apps](https://docs.microsoft.com/en-us/azure/container-apps/microservices)
- [Health probes in Azure Container Apps](https://docs.microsoft.com/en-us/azure/container-apps/health-probes?tabs=arm-template)
- [Authentication](https://docs.microsoft.com/en-us/azure/container-apps/authentication)
- [Publish revisions with GitHub Actions in Azure Container Apps](https://docs.microsoft.com/en-us/azure/container-apps/github-actions-cli?tabs=bash)
