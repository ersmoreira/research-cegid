# Docker Compose Network

## Table of Contents
- [Docker Compose Network](#docker-compose-network)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [When Not Use Explicit Networks:](#when-not-use-explicit-networks)
  - [When Use Explicit Networks:](#when-use-explicit-networks)
  - [Summary of Differences:](#summary-of-differences)
  - [When to Use Explicit Networks?](#when-to-use-explicit-networks)


## Introduction

In Docker Compose, the use of networks versus not using explicit networks can affect how containers communicate with each other and with the outside world.

## When Not Use Explicit Networks:

- Default network: Docker Compose automatically creates a default network for services (containers), allowing them to communicate directly with each other.

- Hostname resolution: All containers in a Compose project can communicate with each other using the service names as DNS (hostname).  
  For example, if you have a `web` service and a `db` service, the `web` service can connect to the `db` service using the hostname `db`.

  ```yaml
  services:
    web:
      image: nginx
    db:
      image: postgres
  ```

  Even without defining explicit networks, Docker creates a shared network for the project, and both containers can communicate via `db`.

- Isolation: All services are on the same network, meaning all containers can see and connect to each other.  
  However, they are isolated from other containers outside the project or other Compose projects, as each project has its own default network.

## When Use Explicit Networks:

When you explicitly define networks in the `docker-compose.yml` file, you gain more control over how services communicate.  

Example with Defined Networks:

```yaml
services:
  web:
    image: nginx
    networks:
      - front_net

  db:
    image: postgres
    networks:
      - back_net

networks:
  front_net:
  back_net:
```

- Custom network: Here, was created two separate networks: `front_net` and `back_net`.  
  The `web` and `db` services are on different networks, meaning they cannot communicate directly (unless add both to the same network).

- Stronger isolation: Defining networks explicitly allows to isolate services more granularly.  
  For example, can prevent `web` from accessing `db` by placing them in different networks.

- Multi-network: A container can be connected to multiple networks, which can be useful for organizing communication.  
  In the example below, `web` is connected to two networks, allowing access to both `db` and the outside world.

  ```yaml
  services:
    web:
      image: nginx
      networks:
        - front_net
        - back_net

    db:
      image: postgres
      networks:
        - back_net

  networks:
    front_net:
    back_net:
  ```

- DNS resolution: As with the case without explicit networks, services can still communicate using the service name as the hostname within the same network.

---

## Summary of Differences:

- Communication and isolation:
   - Without explicit network: All containers in a Docker Compose project can communicate automatically.
   - With explicit network: You can control precisely which services can communicate by creating different networks to isolate the services.

- Control and flexibility:
   - Without explicit network: It’s simple, with less configuration, and is suitable for small or less complex projects.
   - With explicit network: It offers more control, ideal for larger projects where network isolation and segmentation are necessary.

- DNS resolution:
   - In both cases, Docker provides hostname resolution by default for communication between containers on the same network.

- Inter-project connection:
   - Without explicit network: Each Docker Compose project is isolated with its own default network.
   - With explicit network: You can share networks between different projects or services, allowing containers from different Compose setups to connect.

---

## When to Use Explicit Networks?

- If need stronger isolation between services.
- If  want to share networks between multiple projects or containers.
- When need fine-grained control over communication between services.
