# Research Docker

## Table of Contents
- [Research Docker](#research-docker)
  - [Table of Contents](#table-of-contents)
  - [1. Introduction to Docker](#1-introduction-to-docker)
  - [2. Docker Core Components](#2-docker-core-components)
  - [3. Essential Docker Commands](#3-essential-docker-commands)
  - [4. Working with Docker Compose](#4-working-with-docker-compose)
  - [5. Important Concepts](#5-important-concepts)
  - [6. Best Practices](#6-best-practices)

## 1. Introduction to Docker

- **What is Docker?**  
Docker is a software platform that enables the creation, management, and running of applications in containers. 
<br>
- **What is a container?**
Containers are isolated environments that include everything an application needs to run, ensuring consistency across different environments.
<br>
- **Advantages:**
  - Portability (runs in any environment)
  - Efficiency (resource sharing between containers)
  - Consistency (isolated dependencies)
  - Scalability (easy replication of containers)
  
## 2. Docker Core Components

- **Docker Engine**  
   The Docker Engine is the core of Docker, responsible for building and running containers.
<br>

- **Docker Image**  
   An image is a packaged set of files and dependencies needed to run an application. Images are immutable and are used as a base to run containers.

     ```bash
     docker pull nginx
     ```
<br>

- **Docker Container**  
   A container is an instance of an image. It is an isolated process running the application.

   - **Example command to run a container**:
     ```bash
     docker run -d -p 80:80 nginx
     ```
     Here, `-d` runs the container in detached mode, and `-p` maps the local port (80) to the container port (80).
<br>

- **Dockerfile**  
   A Dockerfile is a script containing the instructions required to create a Docker image. It includes commands such as copying files, installing packages, configuring the environment, etc.

   - **Example of a Dockerfile**:
     ```dockerfile
     FROM ubuntu:20.04
     RUN apt-get update && apt-get install -y nginx
     CMD ["nginx", "-g", "daemon off;"]
     ```
<br>

- **Docker Compose**  
   Docker Compose allows defining and managing multi-container applications. Instead of manually starting and configuring multiple containers, you create a `docker-compose.yml` file to describe how containers interact and are configured.

   - **Example of a `docker-compose.yml`**:
     ```yaml
     services:
       web:
         image: nginx
         ports:
           - "80:80"
     ```

## 3. Essential Docker Commands

- **Images**:
   - **List local images:**
     ```bash
     docker images
     ```
   - **Pull an image from Docker Hub:**
     ```bash
     docker pull <image_name>
     ```
   - **Remove an image:**
     ```bash
     docker rmi <image_id>
     ```
<br>

- **Containers**:
   - **List running containers:**
     ```bash
     docker ps
     ```
   - **Start a container:**
     ```bash
     docker start <container_id>
     ```
   - **Stop a container:**
     ```bash
     docker stop <container_id>
     ```
   - **Remove a container:**
     ```bash
     docker rm <container_id>
     ```
<br>

- **Volumes**:
   - **Create volumes for data persistence:**
     ```bash
     docker volume create <volume_name>
     ```
   - **List volumes:**
     ```bash
     docker volume ls
     ```
   - **Mount a volume in a container:**
     ```bash
     docker run -v <volume_name>:<destination_directory> <image>
     ```

## 4. Working with Docker Compose

Docker Compose is ideal for setting up multiple services in containers. Below is an example of a web application using Nginx as the front-end and MySQL as the database.

  - **Example of `docker-compose.yml`:**
    ```yaml
    services:
      web:
        image: nginx
        ports:
          - "8080:80"
        volumes:
          - ./html:/usr/share/nginx/html
        depends_on:
          - db

      db:
        image: mysql:5.7
        environment:
          MYSQL_ROOT_PASSWORD: example
    ```

  - **Compose Commands**:
    - **To start the application**:
    ```bash
    docker-compose up
    ```
    - **To stop the containers:**
    ```bash
    docker-compose down
    ```
<br>

## 5. Important Concepts

- **Volumes and Data Persistence**  
   Containers are ephemeral, meaning that if a container is removed, the data inside is lost. To ensure data persistence, we use volumes that allow storing data outside the container lifecycle.
<br>

- **Docker Networking**  
   Docker allows the creation of networks so that containers can communicate with each other. Docker automatically creates a "bridge" network, but it is also possible to configure custom networks.
    <br>

   - **Create a custom network**:
     ```bash
     docker network create my_network
     ```
     
   - **Connect a container to the network**:
     ```bash
     docker run -d --network=my_network --name my_app nginx
     ```
     <br>

- **Building Custom Images**  
   Using a Dockerfile, you can create custom images with your application and its dependencies.

   - **Command to build an image from a Dockerfile**:
     ```bash
     docker build -t my_image .
     ```
<br>

## 6. Best Practices

- **Keep images lightweight**: Use minimal base images (e.g., `alpine`) whenever possible to reduce size and improve performance.
<br>

- **Dockerfile layers**: Each instruction in the Dockerfile creates a layer. Minimize the number of layers to keep images small.
<br>

- **Remove unnecessary dependencies**: After installing temporary packages during the build, remove them to avoid including unnecessary files in the final image.
<br>

- **Use volumes for persistent data**: Don’t store critical data inside containers; use volumes to ensure data persistence.