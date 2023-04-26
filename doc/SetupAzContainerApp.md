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

# Resources

- [Container App QuickStarts](https://learn.microsoft.com/en-us/azure/container-apps/get-started?tabs=bash)
- [Container App Networking](https://learn.microsoft.com/en-us/azure/container-apps/networking)
- [Provider a VNet to an external container app environment](https://learn.microsoft.com/en-us/azure/container-apps/vnet-custom?pivots=azure-portal&tabs=bash)