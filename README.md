# API Demo

A simple demonstration of how to dockerize an app, build and run it. This demo is based on the [Getting Started](https://docs.docker.com/get-started) tutorial at Docker.

## Docker Setup

To pull and run ths project from Docker:

```
# Tell Docker to pull apidemo repository with tag 'latest' and run it 
# locally on port 8000. 80 represents the port config for the app.
docker run -p 8000:80 protosac/apidemo:latest
```

## Instructions

To create a docker image, we need an app so we'll make one. Each app below is the same written in different languages. Use whichever is most comfortable for you, as the leson focuses on Docker, not the language or framework.

- [Java Instructions]()
- [Javascript Instructions]()
- [Python Instructions]()