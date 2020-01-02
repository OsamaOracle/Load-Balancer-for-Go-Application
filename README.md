# Local Deployment with Docker
## Requirements
- [Docker](https://docs.docker.com/v17.09/engine/installation/)

## Setup
We have a docker compose file for setting up our applications.
Simply run following command to up our containers in root directory
```\bash
docker-compose up --build
```


# Architecture
![Architecture] ==> https://architecturetask.s3.eu-west-2.amazonaws.com/architecture.png
## Services
1. Load Balancer
2. Backend Applications

## Components
### Docker Containers
1. Load Balancer - Nginx
2. Java Application
3. Golang Application

To setup and start all components, we have a docker compose file which defines each service and make it easy to manage all our containers as a single service.

We have separate Dockerfiles for each of service which helps to setup our application in a container.

Both applications are binding on 8080 port internally (inside docker) and Load Balancer service is binding on 8080 to host machine.

1. Nignx (Load Balancer) - We are utilizing Nginx here as a load balancer service for our application which helps us to scale out between applications and provide weightage parameter for our requirement of load management.
Along with common configuration of Nginx, we are using `weight` parameter for distributing load - 70% to Go Application and 30% to Java Application
2. Applications - Each application is build to a final binary and we are just wrapping that build into a container and starting the service
    Base images for
    - Golang Application - golang:1.12-alpine
    - Java Application - adoptopenjdk/openjdk11:latest
# Size of Images
- Base Image size
    ```
    golang                       1.12-alpine                 346MB
    nginx                        latest                      126MB
    adoptopenjdk/openjdk11       latest                      422MB
    ```
- Composed Image Size
    ```
    compose_load_balancer        latest                   126MB
    compose_golang_application   latest                   370MB
    compose_java_application     latest                   440MB
    ```

## Redirect with LB
As it was mentioned in the doc regarding `/` API
```
/               A static site. Should *not* appear in the final setup as it is but redirect to */hotels*
```
so for this I have written a simple `rewrite`(redirect) rule in Nginx config.


## solution description
- `docker-compose.yml` file in root directory is the main compose file for setting up containers for all services
- `go-app` directory contains binary of Golang Application and `Dockerfile` of relavant setup
-  `java-app` directory contains binary of Java Application and `Dockerfile` of relavant setup
-  `load-balancer` directory contains `nginx.conf` file which is configuration file of Nginx and have load balancer rules written in it.
    And containers a `Dockerfile` for setting up `Nginx` with defined configurations