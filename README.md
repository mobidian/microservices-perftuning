# Microservices Perf Reference Implementation
Microsoft patterns & practices

This reference implementation shows a set of best practices for doing performance analysis in a microservices architecture on Microsoft Azure, using Kubernetes.

## Deploying the Reference Implementation

> **DISCLAIMER:**
> Use of Cosmos DB will incur charges against your Azure account. Please be aware of these charges and understand any activity against the Azure instance will result in fees.

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

export COLLECTION_THROUGHPUT=50000 && \
export NUMBER_OF_DOCUMENTS=14000000 && \
export NUMBER_OF_DOCUMENTS_EXP_FACTOR=0 && \
export NUMBER_OF_BATCHES=110 && \
export COLLECTION_PARTITION_KEY="/partitionKey" && \
export DOCUMENT_TYPE_NAME="InternalDroneUtilization"

# fan out queries dataset
export DATABASE_NAME="invoicing-cp" && \
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

# reduce the amount resource units
az cosmosdb collection update \
   -g $RESOURCE_GROUP \
   --name $DRONESCHEDULER_COSMOSDB_NAME \
   --db-name $DATABASE_NAME \
   --collection-name $COLLECTION_NAME \
   --throughput 2000

# frequent partition key dataset (optimize queries)
export DATABASE_NAME="invoicing-sp" && \
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

# reduce the amount resource units
az cosmosdb collection update \
   -g $RESOURCE_GROUP \
   --name $DRONESCHEDULER_COSMOSDB_NAME \
   --db-name $DATABASE_NAME \
   --collection-name $COLLECTION_NAME \
   --throughput 2000
```

### Create Invoice ingress

```bash
# Export tls data
export INVOICE_INGRESS_TLS_SECRET_NAME=invoice-ingress-tls

helm install ./charts/invoice/ \
     --set ingress.hosts[0].name=$EXTERNAL_INGEST_FQDN \
     --set ingress.hosts[0].tls=true \
     --set ingress.hosts[0].tlsSecretName=$INVOICE_INGRESS_TLS_SECRET_NAME \
     --set ingress.tls.secrets[0].name=$INVOICE_INGRESS_TLS_SECRET_NAME \
     --set ingress.tls.secrets[0].key="$(cat ingestion-ingress-tls.key)" \
     --set ingress.tls.secrets[0].certificate="$(cat ingestion-ingress-tls.crt)" \
     --set reason="Initial deployment" \
     --set tags.dev=true \
     --namespace backend-dev \
     --name invoice-v0.1.0-dev \
     --dep-up

# Verify ingress is created
helm status invoice-v0.1.0-dev
```

### Execute the load test

#### First pass

Navigate to [Load Test readme and follow the
instructions](./src/loadtests/readme.md)

#### Enable feature to optimze queries

```bash
# TODO
```

#### Second pass

Navigate to [Load Test readme and follow the
instructions](./src/loadtests/readme.md)

---

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
