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


### Import Invoicing dataset

> Note: it is recommended to be executed from Azure Cloud Shell

```bash
# Extract resource details from deployment

export DATABASE_NAME="invoicing" && \
export COLLECTION_THROUGHPUT=50000 && \
export NUMBER_OF_DOCUMENTS=14000000 && \
export NUMBER_OF_DOCUMENTS_EXP_FACTOR=0 && \
export NUMBER_OF_BATCHES=110 && \
export COLLECTION_PARTITION_KEY="/partitionKey" && \
export DOCUMENT_TYPE_NAME="InternalDroneUtilization"

# fan out queries dataset
export COLLECTION_NAME="utilization-cp" && \
dotnet run \
       --project $PROJECT_ROOT/src/shipping/dronescheduler/Fabrikam.DroneDelivery.DroneSchedulerService.BulkImport/Fabrikam.DroneDelivery.DroneSchedulerService.BulkImport \
       --auth-key=$AUTH_KEY \
       --endpoint-url=$ENDPOINT_URL \
       --database-name=$DATABASE_NAME \
       --collection-name=$COLLECTION_NAME \
       --collection-partition-key=$COLLECTION_PARTITION_KEY \
       --collection-throughput=$COLLECTION_THROUGHPUT \
       --document-type-name=$DOCUMENT_TYPE_NAME \
       --flatten-partition-key=true \
       --number-of-batches=$NUMBER_OF_BATCHES \
       --number-of-documents=$NUMBER_OF_DOCUMENTS \
       --number-of-documents-exp-factor=$NUMBER_OF_DOCUMENTS_EXP_FACTOR


# frequent partition key dataset (optimize queries)
export COLLECTION_NAME="utilization-sp" && \
dotnet run \
       --project $PROJECT_ROOT/src/shipping/dronescheduler/Fabrikam.DroneDelivery.DroneSchedulerService.BulkImport/Fabrikam.DroneDelivery.DroneSchedulerService.BulkImport \
       --auth-key=$AUTH_KEY \
       --endpoint-url=$ENDPOINT_URL \
       --database-name=$DATABASE_NAME \
       --collection-name=$COLLECTION_NAME \
       --collection-partition-key=$COLLECTION_PARTITION_KEY \
       --collection-throughput=$COLLECTION_THROUGHPUT \
       --document-type-name=$DOCUMENT_TYPE_NAME \
       --flatten-partition-key=false \
       --number-of-batches=$NUMBER_OF_BATCHES \
       --number-of-documents=$NUMBER_OF_DOCUMENTS \
       --number-of-documents-exp-factor=$NUMBER_OF_DOCUMENTS_EXP_FACTOR
```





---

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
