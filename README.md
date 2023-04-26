# gcamp2023-containercamp

# Objective

Supply a container-based deployment with

- An entry reverse proxy
- An API delivering Weather Forecasts
- An API receiving current weather conditions
- A database to storing the weather conditions

# Individual Containers

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
   curl http://localhost:3000/posts
   ```
2. Insert item into a collection `posts`
   ```bash
   curl http://localhost:3000/posts/ -X POST -d '{"title": "json-server","author": "typicode"}'
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