## Docker Compose

#### 1. Docker compose file format

Indentation: 2 blanks

All named volumes should be listed in top level `volume:`. For annonymous volumes and bind mounts, you don't need to list them outside of services.

You don't need to create network for multi containers, because all services in the file will share a network by default.

```yaml
version: "3.4"

services:
  # client:
  #   image: nginx-client
  #   build:
  #     context: .
  #     target: nginx-client
  #   restart: always
  #   ports:
  #     - 3080:80
  #   volumes:
  #     - /client/node_modules
  #   depends_on:
  #     - api
  api:
    container_name: LibreChat
    ports:
      - 3080:3080               # Change it to 9000:3080 to use nginx
    depends_on:
      - mongodb
    image: librechat                # Comment this & uncomment below to build from docker hub image
    build:                                   # ^------
      context: .                              # ^------
      target: node                             # ^------v
    # image: ghcr.io/danny-avila/librechat:latest # Uncomment this & comment above to build from docker hub image
    restart: always
    extra_hosts: # if you are running APIs on docker you need access to, you will need to uncomment this line and next
    - "host.docker.internal:host-gateway"
    env_file:
      - .env
    environment:
      - HOST=0.0.0.0
      - MONGO_URI=mongodb://mongodb:27017/LibreChat
      # - CHATGPT_REVERSE_PROXY=http://host.docker.internal:8080/api/conversation # if you are hosting your own chatgpt reverse proxy with docker
      # - OPENAI_REVERSE_PROXY=http://host.docker.internal:8070/v1/chat/completions # if you are hosting your own chatgpt reverse proxy with docker
      - MEILI_HOST=http://meilisearch:7700
      - MEILI_HTTP_ADDR=meilisearch:7700
    volumes:
      - /app/client/node_modules
      - ./api:/app/api
      - ./.env:/app/.env
      - ./.env.development:/app/.env.development
      - ./.env.production:/app/.env.production
      - /app/api/node_modules
      - ./images:/app/client/public/images
  mongodb:
    container_name: chat-mongodb
    # ports:  # Uncomment this to access mongodb from outside docker, not safe in deployment
    #   - 27018:27017
    image: mongo
    restart: always
    volumes:
      - ./data-node:/data/db
    command: mongod --noauth
  meilisearch:
    container_name: chat-meilisearch
    image: getmeili/meilisearch:v1.0
    restart: always
    # ports: # Uncomment this to access meilisearch from outside docker
    #   - 7700:7700 # if exposing these ports, make sure your master key is not the default value
    env_file:
      - .env
    environment:
      - MEILI_HOST=http://meilisearch:7700
      - MEILI_HTTP_ADDR=meilisearch:7700
      - MEILI_NO_ANALYTICS=true
    volumes:
      - ./meili_data:/meili_data

```

#### 2. Start and stop multi-containers

```shell
docker-compose up

// To run in detach mode:
docker-compose up -d 

// To shutdown all containers. By default, containers were removed after shutting down
docker-compose down

// To remove volumes when shutting down containers
docker-compose down -v

// Only build images, don't start containers
docker-compose build
```

#### 3. Attach yourself to the container

```shell
docker run iit -d node

docker container attach CONTAINER_NAME

// Execute command inside of a container
docker exec -it CONTAINER_NAME npm init

docker run -it node npm init

docker run -it -v /Users/xuhi/CodingProjects/app:/app node-util npm init
```

docker exec is good for you to read log files.

#### 4. CMD vs. ENTRYPOINT

#### 5. Utility Container

#### 6. Replace -it option

in docker compose file, you can use:

```yaml
service:
  npm:
    build: ./
    stdin_open: true
    tty: true
```

```shellDocker-compose run
docker-compose run --rm npm init
```

#### 7. Install docker on AWS EC2

```shell
sudo yum update -y
sudo amazon-linux-extras install docker
sudo service docker start

docker ps

// Deploy containers on EC2
docker build .
docker login
docker push ....
docker run -d --rm -p 80:80 ....
docker ps

```



