# Setup Azure Container App

Describes the procedure, using the existing docker images, to create a suitable azure container app environment.

All the operations will be carried-out using Az CLI.

# Setup Container App Environment

```bash
az login
```

```bash
RESOURCE_GROUP="rg-devcamp2023-container-gbo-lab"
LOCATION="westeurope"
CONTAINERAPPS_ENVIRONMENT="cappenv-devcamp2023-gbo-lab"
VNET_NAME="vnet-devcamp2023-gbo-lab"
```
## Custom VNet
```bash
az network vnet create \
  --resource-group $RESOURCE_GROUP \
  --name $VNET_NAME \
  --location $LOCATION \
  --address-prefix 10.0.0.0/16
```

```bash
az network vnet subnet create \
  --resource-group $RESOURCE_GROUP \
  --vnet-name $VNET_NAME \
  --name infrastructure-subnet \
  --address-prefixes 10.0.0.0/21
```

Fetch the infrastructure subnet id
```bash
INFRASTRUCTURE_SUBNET=`az network vnet subnet show --resource-group ${RESOURCE_GROUP} --vnet-name $VNET_NAME --name infrastructure-subnet --query "id" -o tsv | tr -d '[:space:]'`
```

## Setup Private DNS 
Follow the instruction here: [Deploy a private DNS](https://learn.microsoft.com/en-us/azure/container-apps/vnet-custom?pivots=azure-cli&tabs=bash#deploy-with-a-private-dns)


## Create the container app with a managed identity granted the permission to pull from acr.

- Create an user assigned identity
  ```bash
  az identity create \
  --name $IDENTITY \
  --resource-group $RESOURCE_GROUP
  ```

- Setup the environment variables
  ```bash
    REGISTRY_NAME="acrgdc2023cntrlab"
    CONTAINERAPP_NAME="capp-gcamp2023-demo5-rp"
    IMAGE_NAME="gcamp2023/demo5/rp"
    IDENTITY="uaid-gcamp2023-container-lab-cappenv"
  ```

- Add the container app
    ```bash
    az containerapp create \
    --name $CONTAINERAPP_NAME \
    --resource-group $RESOURCE_GROUP \
    --environment $CONTAINERAPPS_ENVIRONMENT \
    --user-assigned $IDENTITY_ID \
    --registry-identity $IDENTITY_ID \
    --registry-server "$REGISTRY_NAME.azurecr.io" \
    --image "$REGISTRY_NAME.azurecr.io/$IMAGE_NAME
    ```

# Resources

- [Container App QuickStarts](https://learn.microsoft.com/en-us/azure/container-apps/get-started?tabs=bash)
- [Container App Networking](https://learn.microsoft.com/en-us/azure/container-apps/networking)
- [Provider a VNet to an external container app environment](https://learn.microsoft.com/en-us/azure/container-apps/vnet-custom?pivots=azure-portal&tabs=bash)