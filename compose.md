# What is Docker Compose?
  - Docker Compose is a tool used to manage and run multiple containers on a single machine using one YAML file (compose.yml).
  - It is especially useful for working with multiple microservices during local development.
  - Instead of manually running build/run commands for every update, developers can simply use:
    ```
    docker-compose up
    docker-compose down
    ```
  # Used For:
    1 Local development
    2 Application testing
    3 Running small applications on a single host

 ## How docker swarm is different from docker compose.
  - Docker swarm and docker compose are total different
   ```
     - docker compose is only used to build and run the images in a single host by using single yaml file(docker-compose.yml)
     - docker swarm is where it will handle containers accross multiple hosts as a cluster.
   ```
  #### To simply put:
        . DOCKER-COMPOSE: One Machine Orchestration
            A tool that manages multiple containers on one host using a YAML file. Ideal for local development and testing.
        . Docker-swarm: Many Machines Orchestration
            Docker’s built‑in orchestrator that manages containers across many hosts, providing scaling, load balancing, and high availability.

## Explanation using the project: 3‑Tier Architecture Using Docker Compose nginx + nodejs + redis
   Check out this repo:- https://github.com/docker/awesome-compose/tree/master/nginx-nodejs-redis
   
  ### Project Overview
      - This project demonstrates a simple 3‑tier architecture:
```
NGINX → Frontend (Reverse Proxy / Load Balancer)
Node.js → Backend
Redis → Data layer (stores request count)
```
When the user loads the page, the app displays:

  - The number of times the page has been requested
  - The backend container that served the request


  ### Project Structure
            nginx-nodejs-redis
            │
            ├── compose.yml
            │
            ├── web
            │   ├── server.js
            │   ├── package.json
            │   ├── package-lock.json
            │   ├── Dockerfile
            │   └── .gitignore
            │
            └── nginx
                ├── Dockerfile
                └── nginx.conf


### Component Details
#### NGINX
**nginx.conf**
```
Listens on port 80
Forwards incoming requests to the backend on port 5000
Acts as a reverse proxy
Performs load balancing between web1 and web2
```
**Dockerfile**
```
Builds the NGINX image used in docker-compose.
```
#### Web (Node.js Backend)
**server.js**
```
Main backend logic
Connects to Redis
Increments request count
Runs on port 5000
```
**package.json**
```
Defines dependencies
Contains project metadata
Specifies how the app starts
```
**package-lock.json**
```
  Locks exact dependency versions
```
**Dockerfile**
```
    Builds the Node.js application image
```

#### Docker Compose File
```
services:
  redis:
    image: 'redislabs/redismod'
    ports:
      - '6379:6379'

  web1:
    restart: on-failure
    build: ./web
    hostname: web1
    ports:
      - '81:5000'

  web2:
    restart: on-failure
    build: ./web
    hostname: web2
    ports:
      - '82:5000'

  nginx:
    build: ./nginx
    ports:
      - '8080:80'
    depends_on:
      - web1
      - web2
```
```
redis → Runs using redislabs/redismod
web1 / web2 → Node.js backend containers
nginx → Accessible on port 8080
depends_on ensures NGINX starts only after both web containers are running
```
### Commands for compose:
```
Start all containers:
   docker-compose up
Stop and remove all containers:
   docker-compose down 
Access the application:
   http://PUBLIC-IP:8080
```

## Port Mapping:
When running applications inside containers, users cannot directly access internal container ports.<br>
So port mapping is used to connect with the internal containers<br>
### Format: <br>
 **HOST_PORT : CONTAINER_PORT** <br>
<img width="419" height="371" alt="{8D3B6DA9-FC3B-408E-9473-031DDDE69661}" src="https://github.com/user-attachments/assets/c9448c33-953f-47db-8b0f-3a2aa03a84a8" />

### Example:
If Jenkins runs inside the container on port 8080, map it like this:<br>
  **docker run -p 6767:8080 <image-name>**<br>
Now the application is accessible at: **http://PUBLIC-IP:6767**
