# gcamp2023-containercamp

# Objective

Supply a container-based deployment with

- An entry reverse proxy
- An API delivering Weather Forecasts
- An API receiving current weather conditions
- A database to storing the weather conditions

# Individual Containers

## API

### Build

```bash
cd src/weatherforecast/
docker build -t gcamp2023/demo5/api .
```

### Run
```sh
docker run -d -p:32778:80 -e "ASPNETCORE_ENVIRONMENT=Development" -e "ASPNETCORE_URLS=http://+:80" gcamp2023/demo5/api
```

### Consume
<http://localhost:32778/swagger/index.html>

## Reverse Proxy

Based on [nginx](https://hub.docker.com/_/nginx/)

### Build

```bash
cd src/reverse-proxy/
docker build -t gcamp2023/demo5/rp .
```

### Run
```sh
docker run -d -p 80:80 gcamp2023/demo5/rp
```

### Consume

Using a browser, Postman or curl

#### Weather Forecast API

```bash
curl -X GET http://localhost:80/api/weather/forecast
```

#### Weather Condition API

1. Submit condition (gets stored ins DB)
   ```bash
   curl -X POST http://localhost:80/api/weather/condition -d 'Sunny'
   ```
1. Retrieve the condition
   ```bash
   curl -X GET http://localhost:80/api/weather/condition
   ```

## Database

Based on [json-server](https://github.com/typicode/json-server)

### Build

```bash
cd src/db/
docker build -t gcamp2023/demo5/db .
```

### Run

```sh
docker run -d -p 3000:80 -v ~/temp/gcamp-demo/:/opt/data/ gcamp2023/demo5/db
```

### Consume

1. Fetch all the items from the `posts` collection
   ```bash
   curl http://localhost:80/conditions
   ```
2. Insert item into a collection `posts`
   ```bash
   curl http://localhost:80/conditions -X POST -d '{"Sky": "cloudy"}'
   ```

# Docker Compose

Docker compose help to run multiple containers-based application into one single deployment.

The `docker-compose.yml` file describes how to run the individual containers, their inter-connections and their interaction with the outside world (volumes, ports...).

## Build

Their is not build

## Run

After having built all the dependencies

- `gcamp2023/demo5/rp`
- `gcamp2023/demo5/db`
- `gcamp2023/demo5/api`

Within the directory where the `docker-compose.yml` resides, here `deploy/docker-compose.yml`.

```bash
docker-compose up -d
```

- `-d` for daemon, non-interactive execution
- `up` reads the `docker-compose` manifest and build the container.

### Tearing down
```bash
docker-compose down
```

Will halt, destroy and remove all the containers being defined by the compose manifest.

# Using Azure Container App

Using [Azure Container Apps](https://learn.microsoft.com/en-us/azure/container-apps/overview).

1. Pushes the generated container image to a given Azure Container Registry
2. Create a Container App Environment
3. Create the Container Apps for the individual container images

## Azure Container Registry

Given the `rg-devcamp2023-container-lab` resource group exists and you're granted at least the _contributor_ role

1. Create the registry
   ```bash
   az acr create --name acrgdc2023cntrlab --sku Basic -g rg-devcamp2023-container-lab
   ```
2. Login the local docker context
   ```bash
   az login -t garaio.com
   az acr login --name acrgdc2023cntrlab
   ```
3. Tag the container images to assign them the to the registry. Given the image have been already built.
   ```bash
   docker tag gcamp2023/demo5/rp acrgdc2023cntrlab.azurecr.io/gcamp2023/demo5/rp
   docker tag gcamp2023/demo5/db acrgdc2023cntrlab.azurecr.io/gcamp2023/demo5/db
   docker tag gcamp2023/demo5/api acrgdc2023cntrlab.azurecr.io/gcamp2023/demo5/api
   ```

4. Push the images to the registry
   ```bash
   docker push acrgdc2023cntrlab.azurecr.io/gcamp2023/demo5/rp
   docker push acrgdc2023cntrlab.azurecr.io/gcamp2023/demo5/db
   docker push acrgdc2023cntrlab.azurecr.io/gcamp2023/demo5/api
   ```

# Deploy as app service using docker compose

1. Create the storage account for the database
   ```bash
   az storage account create --resource-group rg-devcamp2023-container-lab --name sadevcamp2023contlab --location switzerlandnorth --sku Standard_LRS
   ```

2. Create the file share in the storrage account
   ```bash
   az storage share create --name dbshare-devcamp2023-container-lab --account-name sadevcamp2023contlab
   ```

3. Create the appservice plan
   ```bash
   az appservice plan create --name ap-devcamp2023-container-lab --resource-group rg-devcamp2023-container-lab --sku S1 --is-linux
   ```

4. Create the webapp with the docker compose file
   ```bash
   cd deploy-azure
   az webapp create --resource-group rg-devcamp2023-container-lab --plan ap-devcamp2023-container-lab --name devcamp2023-container-webapp --multicontainer-config-type compose --multicontainer-config-file docker-compose.yml
   ```