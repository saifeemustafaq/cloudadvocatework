High-level Azure Static Web Apps works by the following components being involved:

- A repository on a version control system like GitHub.
- An Azure Static Web App resource on Azure.

The Azure service is connected to GitHub via GitHub actions and can listen to repository changes. When a repository change comes in, the source code can then be deployed, or redeployed to Azure.

The following components are what makes everything work:

- Azure Account. An Azure account is needed so you’re able to provision the cloud resources needed to host your app.
- Azure static apps resource. Hosting your files is dependent on their being a provisioned resource of Azure Static Web app.
- Workflow provider. You can use many different providers for your workflow. The idea is that you make a code change and push it and said code gets deployed. Available providers are GitHub. Azure DevOps, GitLab and Bitbucket.
  A code repository. You need a Git repository that lives on GitHub, to store your application files.
- A config file. A yaml config file is added the first time you connect your repo to the service. There's also another config file you can add to set up things like routing and authorization among other things.

:::image type="content" source="../media/GitHub-to-azure.png" alt-text="Integration between GitHub and Azure":::

## Progressively enhance your app

Azure Static Apps provide static hosting of files. At minimum, you can start off with just hosting HTML, JS and CSS files. Then you can progressively enhance your app by adding a backend to it via Azure Functions, configure auth via Azure Active Directory, AAD or using a social sign-in.

The code repo will get a YAML file that points to locating frontend and backend after deploying the app to a resource.

## Workflow

The workflow of Azure Static Web Apps complies with developer’s daily workflow, which means that every code change is built and deployed on the hosted app.
NOTE: Azure Static Web Apps will manage the provisioning of all Azure resources on your behalf.

There are several flows you need to know about:

- First deployment, when a static web app resource is created in the Azure portal, via Visual Studio Code or via the CLI, Azure links it with a GitHub action workflow to regulate any updates in the code. The workflow starts right away, and takes care of creating and publishing the app.
- Review version. Using pull requests, you'll be able to create a review version of your app. This enables you to inspect the site and evaluate the change before it’s released to production.
- All future repository changes. For every new commit or pull request, the GitHub action will build and deploy the web app. The workflow is also triggered if a change is made to the repository's watched branch. It’s configurable what type of repository change will lead to triggering a GitHub action that leads to a redeployment.

## API support with Functions

Azure Static Web Apps provides serverless API endpoints via Azure Functions.

It has the following features:

- **Integrated security**. It integrates with authentication and you’re able to set up roles to access various logical areas of your API.
- **Seamless routing**. The API is set up in such a way that’s it made available to the web app without needing specific CORS rules.

There’s two different ways to provide the backend, that depends on the hosting plan:

- **Managed functions**. Here, the Azure Functions app is managed by Azure Static Web Apps and follows its deployment lifecycle.
- **Bring your own functions**. You can also provide an Azure Functions app of any plan. However, you’re thereby responsible for that separate deployment.

An API is added by generating an Azure Functions app and letting Azure Static Web Apps know its existence in the Static Web Apps project yml file.
