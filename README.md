# gcamp2023-containercamp

# Objective

Supply a container-based deployment with

- An entry reverse proxy
- An API delivering Weather Forecasts
- An API receiving current weather conditions
- A database to storing the weather conditions


# Database

## Build

```bash
cd src/db/
docker build -t gcamp2023/demo5/db .
```

## Run

Based on [json-server](https://github.com/typicode/json-server)

Run the container

```sh
docker run -d -p 3000:3000 -v ~/temp/gcamp-demo/:/opt/data/ gcamp2023/demo5/db
```

## Consume

1. Fetch all the items from the `posts` collection
   ```bash
   curl http://localhost:3000/posts
   ```
2. Insert item into a collection `posts`
   ```bash
   curl http://localhost:3000/posts/ -X POST -d '{"title": "json-server","author": "typicode"}'
   ```
    