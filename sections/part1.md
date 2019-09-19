# 1: Install Docker

Take 5 minutes to [download and install Docker on your local machine.](https://docs.docker.com/engine/installation/)

Then check to make sure it's up and running:

```
# check docker version
docker --version

# view info about your installation
docker info

# run the test image and view a list of currently installed images
docker run hello-world
docker image ls
docker container ls --all
```
If this doesn't work, let your instructors know.

## Images and Containers

**Images** represent your application package and it's setup and configuration are defined within the Dockerfile.

**Containers** are *running instances* of your application image. 

What makes containers so consistent for you and your team is that Docker handles the environment setup so that all anyone has to do to run the app is run a Docker container.

Let's try it.

## Running a Docker App

Now that you have Docker installed, let's see just how easy it is to use an existing application. Pull and run ths project from Docker:

```
# Tell Docker to pull apidemo repository with tag 'latest' and run it 
# locally on port 8000. 80 represents the port config for the app.
docker run -p 8000:80 lotech/apidemo:latest
```

With this simple command as a developer you were able to avoid:

- cloning a repository
- install dependencies
- configure a server

This is why Docker is a great tool for collaboration and service management. It automates processes to make managing services and deploying them simple and fast.

The application you just ran is an example of what you'll be creating for this lesson. Even though it's pretty minimal, alot of configuration will go into making this work. 

[Continue to the next section ...](sections/part2.md)
