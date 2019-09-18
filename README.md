# API Demo

A simple demonstration of how to dockerize an app, build and run it. This demo is based on the [Getting Started](https://docs.docker.com/get-started) tutorial at Docker.

## Running a Docker App

To pull and run ths project from Docker:

```
# Tell Docker to pull apidemo repository with tag 'latest' and run it 
# locally on port 8000. 80 represents the port config for the app.
docker run -p 8000:80 protosac/apidemo:latest
```

## Creating a Docker Image

To create a docker image, we need an app so we'll make one. Each app below is the same written in different languages. Use whichever is most comfortable for you, as the leson focuses on Docker, not the language or framework.

- [Java App]()
- [Javascript App]()

### 1: Install Docker

[Download and install Docker on your local machine.](https://docs.docker.com/engine/installation/)

Then check to  make sure it's up and running:

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

The difference between images and containers is that images represent your application and are defined with the Dockerfile. Containers are *running instances* of your application. What makes these containers so consistent for you and your team is that Docker handles the environment configuration so that all anyone has to do to run the app is run a Docker container.

### 2: Creating a Docker Image

1. Clone this repository.
2. Add a new file to it named `Dockerfile`
3. Open `Dockerfile` in your code editor.
4. Copy and paste the following into it:

```Dockerfile
# Use an official Python runtime as a parent image
FROM python:3.7

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Read the comments of the file to understand what each command is doing. You will use `Dockerfile`s to define how your projects should run. Everyone on your team will use this to run and/or deploy this app.

Finally, build and run the app: 

```
# think of the tag as the build version for the Image
docker build --tag=myservice .

# here we name the container myservice and run it here
docker run -p 4000:80 myservice

# OR run it in the background instead
docker run -d -p 4000:80 myservice

# to stop a detached container, first get the container ID
docker container ls
# ...then use the stop command
docker container stop paste_contaienr_id_here

```

### 3: Share Images with a Registry

So you've created an image for your application. Now you need to upload it somewhere so that your team can use it and deploy it.

For this, we use a Registry which in plain terms, is a cloud service that hosts all of your docker images. It's a server where the team stores your collection repositories. And each repository can contain any number of docker images. 

If you don't already have a Docker Hub account, you can make one. The hub provides a registry service for free.

1. Go to: https://hub.docker.com/ to create an account
2. In your terminal, login: `docker login`

Finally, let's make sure our image has been created and then push it to the Registry:
```
# look for myservice Repository with Tag latest
docker image ls
# Push!
docker push your_account_name/myservice:latest
```

You should now be able to see your repository online in the Registry! From now on you can pull and run this repository from the CLI instead of locally:

`docker run -p 4000:80 your_account_name/myservice:latest`

### 4: Composing Services

When building microservices, our intention is have each service run independently to provide data services for any client. Using Docker Compose gives us a tool to compose those services.

Before you continue, be sure you have [VirtualBox installed](https://www.virtualbox.org/wiki/Downloads). You'll need it to test our service configurations.

1. Create a new file: `docker-compose.yml`
2. Copy and paste the following, __modifying the repository name with your own__:

```yml
version: "3"
services:
  web:
    # replace username/repo:tag with your name and image details
    image: username/repo:tag
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.1"
          memory: 50M
      restart_policy:
        condition: on-failure
    ports:
      - "4000:80"
    networks:
      - webnet
networks:
  webnet:
```

**DON'T FORGET TO MODIFY THE IMAGE OPTION WITH THE NAME OF YOUR OWN!**

What we've done is defined a Service. Services run multiple tasks (containers) at a time using the configuration defined in `docker-compose.yml`. Let's start our service and observe its behavior. For this exercise, we're creating a service named `getstartedlab` for practice.

```
# start all replicas defined in docker-compose
docker swarm init

# start the service
docker stack deploy -c docker-compose.yml getstartedlab

# view current services
docker stack services ls getstartedlab
docker stack ps getstartedlab

# stop the service and kill the swarm
docker stack rm getstartedlab
docker swarm leave --force
```

- **Swarm**: A group of machines run as a cluster.
- **Swarm Manager**: A single machine within a cluster which executes commands on the others.
- **Stack**: The application.

We'll now build swarm for our application. To do it, we need to create virtual machines which we'll join to a cluster.

Create 2 virtual machines using the `docker-machine` command. It may take a minute for each to be created so be patient.

```bash
# STEP 1
docker-machine create --driver virtualbox myvm1
docker-machine create --driver virtualbox myvm2

# check that both have been created and are running. You'll need both IP addresses for the next step so take note!
docker-machine ls

# STEP 2: Set myvm1 as the manager. REPLACE <myvm1 ip> WITH IP TO THE VM
docker-machine ssh myvm1 "docker swarm init --advertise-addr <myvm1 ip>"

# STEP 3: copy the token printed after the above command and use it:
docker-machine ssh myvm2 "docker swarm join --token <token> <myvm1 ip>:2377"

# view the swarm
docker-machine ssh myvm1 "docker node ls"
```

> NOTE: In Step 3 above notice that we're connecting to myvm2 and connecting to myvm1 ip address! We are telling docker to connect myvm2 as a member of the former.

The last thing we need to do now is deploy our app to the swarm.

```
# check output from the following command. no machines should be active:
docker-machine ls

# Configure our shell to talk to myvm1
docker-machine env myvm1
eval $(docker-machine env myvm1) 

# verify myvm1 is active (you will see a * in the right column)
docker-machine ls

# DEPLOY!
docker stack deploy -c docker-compose.yml getstartedlab
```

**Trouble-shooting**
```
docker-machine restart myvm
docker-machine ls
```