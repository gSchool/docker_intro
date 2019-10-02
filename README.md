# Docker Intro

A simple demonstration of how to dockerize an app, build and run it. This demo is based on the [Getting Started](https://docs.docker.com/get-started) tutorial at Docker.

This lesson aims to teach you the basics of Docker and get you familiar with using the tool. There are 5 sections, each with their own exercises for mastery. By the end you should be able to:

- Create images
- Create containers
- Create services (with Virtual Machines)
- Create and manage clusters (Docker Compose)
- Create stacks (Docker Compose)

Clone this repository (or any of the relevant repos below per your instructors guidance). This lesson focuses strictly on Docker, not a given language or framework.

- [Java App](https://github.com/LinguaTechEdu/docker_intro_java)
- [Javascript App]()

Each repo contains: a single script, an ignore file, a dependencies file (if needed) and a README with these same instructions. 

## [Start the first section](sections/part1.md)


### Trouble-shooting

Here's some common issues you may run into while learning Docker, along with some ways to troubleshoot.

**Tip 1: Docker connection issues**
If the docker server seems to be down or you're getting errors that say docker isn't running, first try closing your terminal session out and starting a new one. Then run `docker info` or `docker version` to check if it's running.

**Tip 2: Docker machine stopped or failed**
If your docker machine (vm) says it's stopped or has failed, first try restarting it. Then verify it restarted.

```
docker-machine restart myvm
docker-machine ls
```

**Tip 3: Error port in use**

You're trying to connect to a port that's already in use. First, if you don't want to stop a stack or container, simply use a different port. This is the simplest thing to do.

If you have stacks running on that port, they will continue to restart containers according to what you instructed in the `docker-compose.yml`. So stopping a container will not work. Therefore, make sure your stack isn't running with `docker stack ls`.

If you see a stack that needs to be removed: `docker stack rm stackname`. Removing the stack should also kill the containers, freeing up the port.
