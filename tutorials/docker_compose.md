# Docker compose brief

Are you tired by executing `docker run`, `docker build`? so are we.

[Docker Compose](https://docs.docker.com/compose/) is a tool for defining and running multi-container Docker applications.
With Compose, you use a YAML file to configure your application's services. 
Then, with a single command, you create and start all the services from your configuration.

Using Compose is essentially a three-step process:

1. Define your app's environment with a `Dockerfile` so it can be reproduced anywhere.
2. Define the services that make up your app in `docker-compose.yml` so they can be run together in an isolated environment.
3. Run `docker compose up` and the Docker compose command starts and runs your entire app.

A `docker-compose.yml` looks like this:

```yaml
services:
  frontend:
    image: frontend:0.0.1
    ports:
      - "8080:8080"

  backend:
    image: backend:0.0.1
    ports:
      - "3000:3000"
```

The given Docker Compose file describes a multi-service application with two services: yolo and ollama-ui.

## Compose benefits 

Using Docker Compose offers several benefits:

- **Simplified Container Orchestration**: Docker Compose allows for the definition and management of multi-container applications as a single unit.
- **Reproducible Environments**: Since compose is defined in a YAML file, it's easy to deploy the same environment in different machine without missing any `docker run` command. This ensures that the application runs consistently across different machines.
- **Automate Volumes and Networking**: Docker Compose automatically creates a network for the application and assigns a unique DNS name to each service. No need to create networks and volumes. 


# Exercises 

### :pencil2: Try compose 


The official Docker Compose website provides good getting started tutorial with Docker Compose, explaining key concepts and demonstrating how to write a Compose file.
Complete the tutorial:      
https://docs.docker.com/compose/gettingstarted/

