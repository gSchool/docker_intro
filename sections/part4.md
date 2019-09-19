# 4: Composing Services

Composing services is about setting up your repositories to self-manage multiple running containers.

When building microservices, our intent is to have each service run **independently**. Using Docker Compose gives us a tool to compose those services.

Before you continue, be sure you have [VirtualBox installed](https://www.virtualbox.org/wiki/Downloads). You'll need it to test our service configurations. Take 5 minutes to set that up. While it's installing:

1. Create a new file: `docker-compose.yml`
2. Copy and paste the following, __modifying the registry, repo and tag name with your own__:

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

**DON'T FORGET TO MODIFY THE `image` PROPERTY WITH THE NAME OF YOUR OWN.**

What we've done is defined a **Service**. Services run multiple **tasks** (containers) at a time using the configuration defined in `docker-compose.yml`. Let's start a new service and observe its behavior. For this exercise, we're creating a service named `apidemolab` for practice.

```
# Step 1: Start all replicas defined in docker-compose.yml
docker swarm init

# Step 2: Start the service.
docker stack deploy -c docker-compose.yml apidemolab

# Step 3: View current services and the status of each replica.
docker stack services apidemolab
docker stack ps apidemolab

# Step 4: Stop the service and kill the swarm
docker stack rm apidemolab
docker swarm leave --force
```

So that's how we start a service. We haven't yet created any clusters, so let's learn a little more abut what they are first.

- **Swarm**: A cluster (group) of machines.
- **Swarm Manager**: A single machine within a cluster which executes commands on the others.
- **Stack**: The service (or a group of services).

Machines refers to each instance of a virtual machine. Docker will spin up new containers for our app on each machine. Docker will then manage those containers together as a cluster. The swarm.

We'll now build a new swarm for our **stack**. To do it, we need to create the virtual machines first. Create 2 virtual machines using the `docker-machine` command, which you'll always use to manage your virtual machines. It may take a minute for each to be created so be patient.

```bash
# STEP 1: Create new virtual machines using virtualbox. Give it a minute to finish.
docker-machine create --driver virtualbox apidemovm1
docker-machine create --driver virtualbox apidemovm2

# check that both have been created and are running. You'll need both IP addresses for the next step so take note!
docker-machine ls

# STEP 2: Set apidemovm1 as the manager for the swarm. REPLACE <apidemovm1 ip> WITH IP TO THE VM. Don't include the tcp:\\ prefix
docker-machine ssh apidemovm1 "docker swarm init --advertise-addr apidemovm1_ip_here:2377"

# STEP 3: copy the command from the output of the previous and use it:
docker-machine ssh apidemovm2 "docker swarm join --token token_here apidemovm1_ip_here:2377"

# view the swarm to run a command on the vm via ssh.
docker-machine ssh apidemovm1 "docker node ls"
```

> NOTE: In Step 3 above notice that we're connecting apidemovm2 to apidemovm1 using apidemovm1's ip address! We are telling docker to connect apidemovm2 as a member of the later.

The last thing we need to do now is deploy our app to the swarm. While running the following commands, take a few minutes to read all the information and understand what it means. Write down your questions and ask them.

```
# Always check output from the following command before proceding. No machines should be active, all should still be running.
docker-machine ls

# Review the environment on the vm. Then configure our shell to talk to it.
docker-machine env apidemovm1
eval $(docker-machine env apidemovm1) 

# verify apidemovm1 is active (you will now see a * in the column)
docker-machine ls

# DEPLOY! Note we are using the -c flag to name the config file and naming the stack apidemolab
docker stack deploy -c docker-compose.yml apidemolab
```

You can use the commands you learned in the Services section to control whether the stack is running or not. You can also view the IP addresses this way, as you'll need them to visit the app.

To view your app, visit the IP address of either of your virtual machines on port 4000. For example: `192.168.99.108:4000` might be the url to a vm. 

[Continue to the next section ...](sections/part5.md)
