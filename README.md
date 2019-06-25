# Microservices Perf Reference Implementation
Microsoft patterns & practices

This reference implementation shows a set of best practices for doing performance analysis in a microservices architecture on Microsoft Azure, using Kubernetes.

## Deploying the Reference Implementation

### Prerequisites

- Azure subscription
- [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
- [Docker](https://docs.docker.com/)
- [Helm 2.12.3 or later](https://docs.helm.sh/using_helm/#installing-helm)
- [JQ](https://stedolan.github.io/jq/download/)

> Note: in linux systems, it is possible to run the docker command without prefacing
>       with sudo. For more information, please refer to [the Post-installation steps
>       for linux](https://docs.docker.com/install/linux/linux-postinstall/)

Clone or download this repo locally.

```bash
git clone --recurse-submodules https://github.com/mspnp/microservices-perf.git
```

The deployment steps shown here use Bash shell commands. On Windows, you can use the [Windows Subsystem for Linux](https://docs.microsoft.com/windows/wsl/about) to run Bash.

### Deploy microservices reference implementation

Navigate to [deployment steps and deploy the microservices reference implementation](./microservices-reference-implementation/deployment.md).





---

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
