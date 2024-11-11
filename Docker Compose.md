
# Docker Compose

## Table of Contents
- [Docker Compose](#docker-compose)
  - [Table of Contents](#table-of-contents)
  - [What?](#what)
  - [Installing](#installing)
    - [Prerequisites:](#prerequisites)
      - [Steps:](#steps)
  - [Commands](#commands)
  - [Clean Up and Reset](#clean-up-and-reset)
    - [Remove all stopped containers, networks, volumes:](#remove-all-stopped-containers-networks-volumes)
    - [Clean Docker System (optional but useful for space management):](#clean-docker-system-optional-but-useful-for-space-management)
  - [Configuration of docker-compose.yml](#configuration-of-docker-composeyml)
  - [Basic Setup](#basic-setup)
  - [Example](#example)



## What?

Docker Compose is a tool for defining and running multi-container Docker applications.  
With a simple YAML configuration file (`docker-compose.yml`), you can specify all the containers, networks, and volumes needed to run your entire application stack.  
Instead of running each Docker container manually, Docker Compose orchestrates the process.


## Installing 

### Prerequisites:
**Docker Desktop**:  
Docker Compose is included with Docker Desktop.  
You don't need to install it separately.
  
#### Steps:
- Download and install **Docker Desktop** from [here](https://www.docker.com/products/docker-desktop/).
- Follow the installation wizard. Make sure to enable **WSL 2** (Windows Subsystem for Linux) during installation for better performance.
- Once installed, you can verify Docker Compose by running the following command:  
   ```bash
   docker-compose --version
   ```

## Commands

| **Command** | **Description** |
|-------------|-----------------|
| `docker-compose up` | Builds, (re)creates, starts containers as per `docker-compose.yml`. |
| `docker-compose up -d` | Runs containers in detached mode (in the background). |
| `docker-compose down` | Stops and removes containers, networks, and volumes. |
| `docker-compose ps` | Lists all running services (containers). |
| `docker-compose logs` | Shows logs from the running services. |
| `docker-compose logs -f` | Follows the logs in real-time. |
| `docker-compose build` | Builds or rebuilds the services without starting them. |
| `docker-compose exec [service] [cmd]` | Executes a command in a running container (e.g., bash shell access). |
| `docker-compose restart [service]` | Restarts a specific service. |
| `docker-compose down --volumes` | Stops and removes containers, networks, and volumes. |
| `docker-compose stop` | Stops all services (containers) without removing them. |
| `docker-compose start` | Starts services (containers) that were previously stopped. |
| `docker-compose rm` | Removes stopped service containers. |
| `docker-compose pull` | Pulls the latest image for the services defined. |


## Clean Up and Reset

Docker Compose leaves behind some resources like **volumes** and **networks** after containers are stopped or removed.  
Here’s how to clean and reset your Docker environment:

### Remove all stopped containers, networks, volumes:

```bash
docker-compose down --volumes --remove-orphans
```

- `--volumes`: Removes all volumes created by services.
- `--remove-orphans`: Removes any extra containers that are not part of the Compose file.

### Clean Docker System (optional but useful for space management):

```bash
docker system prune -a --volumes
```

This command cleans up:
- Stopped containers
- Unused images
- Networks
- Volumes

> **Warning**: The `-a` flag will remove all images, even those not associated with running containers.


## Configuration of docker-compose.yml

The heart of Docker Compose is the `docker-compose.yml` file, where you define the containers (services), networks, and volumes for your application.

## Basic Setup

Let’s create a basic **Node.js** app with **Redis** using Docker Compose on Windows.

- Create a project directory:

```bash
mkdir my-docker-app
cd my-docker-app
```

- Create a directory for the app:

```bash
mkdir app
```

- Create the `docker-compose.yml` file:

```yaml
version: '3.8'

services:
  app:
    image: node:14
    volumes:
      - ./app:/usr/src/app
    ports:
      - "3000:3000"
    networks:
      - app-network
    command: "npm start"  # Command to run when the container starts

  redis:
    image: redis:alpine
    ports:
      - "6379:6379"
    networks:
      - app-network

networks:
  app-network:
```

**Explanation**

- version: Defines the version of Docker Compose.  
  Version 3.8 is recommended for compatibility with the latest Docker versions.
- services: This section defines the services that Docker Compose will manage.  
  In this case, we have two services:
  - app: The Node.js application service.
  - redis: The Redis service.
- image: The Docker image that will be used to create the container.  
  In this case, we're using the official Node.js - image (version 14) and a lightweight version of Redis (alpine).
- volumes: Defines volumes that mount local directories onto the container's file system.  
  In the example, the ./app directory (on the host machine) is mounted to /usr/src/app (inside the container).
- ports: Maps the container’s ports to the host machine’s ports, allowing access to the service through the maped ports.  
  For example, the Node.js app will be available on port 3000 of the host.
- networks: Connects the services to a custom network, enabling them to communicate with each other within the network.  
  Both Redis and Node.js are connected to the app-network.

## Example

- Add a basic Node.js application in the `app` folder:

```bash
cd app
npm init -y
npm install express redis
```

Then, create an `index.js` file in the `app` folder with the following code:

```js
const express = require('express');
const redis = require('redis');

const app = express();
const client = redis.createClient({
  host: 'redis',
  port: 6379
});

client.set('visits', 0);

app.get('/', (req, res) => {
  client.get('visits', (err, visits) => {
    res.send('Number of visits is ' + visits);
    client.set('visits', parseInt(visits) + 1);
  });
});

app.listen(3000, () => {
  console.log('App listening on port 3000');
});
```

- Run the application with Docker Compose:

```bash
docker-compose up
```

Visit `http://localhost:3000` in your browser, and you should see the visit count!


[Go Back](./Index.md)
