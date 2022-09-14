Azure Static Web Apps is a service that can automatically build and deploy web apps to Azure from a code repository. It serves as a go-to service for developers because it's tailored to make changes based on daily workflow that is, any kind of change in the code is immediately reflected on the deployed app.

Azure Static Web Apps work differently than a traditional web server by distributing assets from points around the world. Serverless architecture (via Azure Functions) is used to host API endpoints, which further diminish the need for a backend server.

## Workflow

Here’s how Azure Static apps interact with your code to ensure you can deploy and update your code (if using GitHub as the workflow).

1. On creation of an Azure Static App, the app is deployed from a branch.
2. Azure holds the ability to directly interact with GitHub or Azure DevOps to monitor branches.
3. With every change in the branch, an API gets automatically deployed to Azure Static App.

:::image type="content" source="../media/Azure-static-app-doc.png" alt-text="Image from Azure Static App Doc":::

## Features

There are many compelling features that makes Azure Static Web apps a good choice for your next project.

- **Supports multiple frontend frameworks**. You can build your frontend using a known framework like Angular, React, Svelte, Vue, or Blazor. Azure Static Web Apps can build and leverage the defaults of said framework that makes for a seamless deployment experience.
- **Deployment via integrations**. Azure DevOps and GitHub integration to build according to changes in the repository.
- **Static file hosting**. HTML, CSS, JavaScript web hosting.
- **Opt-in backend**. Azure Functions is what you use to provide backend to your app.
- **Globally distributed content**, accessible from all over the world.
- **Free certs**. Automatically renewable and free SSL certification
- **Supports many security options**. Seamless security with no requirement of CORS.
- **Custom domains**. By default, Azure Static Web Apps provides an auto-generated domain name for your website. However, you can point it to a custom domain.
- **Preview environments**. When you create a pull request, a preview version is created through a temporary URL. This allows you to inspect the app and determine whether to let this change increment should go to production.
- **Multiple ways to authenticate and authorize**. You have access to a series of pre-configured providers including Azure Active Directory, GitHub and Twitter. You can also use register a custom provider.
- **First-class tools**. There’s CLI that lets generate the various parts of the app. It also provides a local debugging experience for API, auth and more, so you can ensure the app works before being deployed. Apart from the CLI, there’s also a Visual Studio Code extension that enables you to deploy your app, among other things.
