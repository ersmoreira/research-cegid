
# Docker Compose

## Table of Contents
- [Docker Compose](#docker-compose)
  - [Table of Contents](#table-of-contents)
  - [What is Docker Compose?](#what-is-docker-compose)
  - [Installing Docker Compose](#installing-docker-compose)
    - [Prerequisites:](#prerequisites)
      - [Steps:](#steps)
  - [Essential Docker Compose Commands](#essential-docker-compose-commands)
  - [Clean Up and Reset](#clean-up-and-reset)
    - [1. **Remove all stopped containers, networks, volumes:**](#1-remove-all-stopped-containers-networks-volumes)
    - [2. **Clean Docker System (optional but useful for space management):**](#2-clean-docker-system-optional-but-useful-for-space-management)
  - [docker-compose.yml Configuration](#docker-composeyml-configuration)
    - [Example:](#example)
  - [Basic Setup and Example](#basic-setup-and-example)
  - [Docker Compose Template](#docker-compose-template)



## What is Docker Compose?

Docker Compose is a tool for defining and running multi-container Docker applications.  
With a simple YAML configuration file (`docker-compose.yml`), you can specify all the containers, networks, and volumes needed to run your entire application stack.  
Instead of running each Docker container manually, Docker Compose orchestrates the process.


## Installing Docker Compose

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

## Essential Docker Compose Commands

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

Docker Compose leaves behind some resources like **volumes** and **networks** after containers are stopped or removed. Here’s how to clean and reset your Docker environment:

### 1. **Remove all stopped containers, networks, volumes:**

```bash
docker-compose down --volumes --remove-orphans
```

- `--volumes`: Removes all volumes created by services.
- `--remove-orphans`: Removes any extra containers that are not part of the Compose file.

### 2. **Clean Docker System (optional but useful for space management):**

```bash
docker system prune -a --volumes
```

This command cleans up:
- Stopped containers
- Unused images
- Networks
- Volumes

> **Warning**: The `-a` flag will remove all images, even those not associated with running containers.

---

## docker-compose.yml Configuration

The heart of Docker Compose is the `docker-compose.yml` file, where you define the containers (services), networks, and volumes for your application.

### Example:

```yaml
version: '3.8'  # Specify the Compose file version

services:
  app:
    image: node:14  # Image of a Node.js app
    ports:
      - "3000:3000"  # Mapping local port 3000 to container's port 3000
    volumes:
      - ./app:/usr/src/app  # Mount local app directory into container
    environment:
      NODE_ENV: development  # Environment variable
    networks:
      - app-network  # Connecting to a network

  redis:
    image: redis:alpine  # Redis image from Docker Hub
    ports:
      - "6379:6379"  # Mapping Redis default port
    networks:
      - app-network  # Same network as the app

networks:
  app-network:  # Defining a custom network
```

---

## Basic Setup and Example

Let’s create a basic **Node.js** app with **Redis** using Docker Compose on Windows.

1. **Create a project directory:**

```bash
mkdir my-docker-app
cd my-docker-app
```

2. **Create a directory for the app:**

```bash
mkdir app
```

3. **Create the `docker-compose.yml` file:**

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

4. **Add a basic Node.js application** in the `app` folder:

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

5. **Run the application with Docker Compose:**

```bash
docker-compose up
```

Visit `http://localhost:3000` in your browser, and you should see the visit count!

---

## Docker Compose Template

Here’s a basic Docker Compose template for quick reference:

```yaml
version: '3.8'

services:
  service_name:
    image: image_name:tag  # e.g., node:14, redis:alpine
    build: ./path/to/dockerfile  # Optional: if building from a Dockerfile
    ports:
      - "host_port:container_port"
    volumes:
      - ./host_dir:/container_dir
    environment:
      ENV_VAR_NAME: value
    depends_on:
      - other_service  # Optional: to start this service after another
    networks:
      - network_name
    command: "start_command"  # Optional: to override the default CMD in the image

networks:
  network_name:  # Defining a custom network
```

---

This guide covers the basics to get started with Docker Compose on Windows. Feel free to adapt and expand on this as you work with more complex Docker environments!
