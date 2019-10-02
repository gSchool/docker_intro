# 4: Composing Services

Composing services is about setting up your repositories to self-manage multiple running containers.

When building microservices, our intent is to have each service run **independently**. Using Docker Compose gives us a tool to compose those services. 

Before you continue, be sure you have [VirtualBox installed](https://www.virtualbox.org/wiki/Downloads). You'll need it to test our service configurations. Take 5 minutes to set that up. And while it's installing:

1. Create a new file: `docker-compose.yml`
2. Copy and paste the following, **modifying the registry, repo and tag name with your own**:

```yaml
version: "3"
services:
  web:
    # REPLACE registry/repo:tag with your registry and repo details
    image: registry/repo:tag
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

**DON'T FORGET TO MODIFY THE `image` KEY WITH THE NAME OF YOUR OWN.**

What we've done is defined a **Service**. Services run multiple **tasks** (containers) at a time using the configuration defined in `docker-compose.yml`. 

Let's start a new service and observe its behavior. For this exercise, we're creating a service named `apidemolab`.

```bash
# Step 1: Start all replicas defined in docker-compose.yml
docker swarm init

# Step 2: Start the service.
docker stack deploy -c docker-compose.yml apidemolab

# Step 3: View current services, then the status of each replica.
docker stack services apidemolab
docker stack ps apidemolab

# Step 4: Stop the service and kill the swarm
docker stack rm apidemolab
docker swarm leave --force
```

So that's how we start a service. We haven't yet created any clusters, so let's learn a little more abut what they are first.

- **Swarm**: A cluster (group) of machines.
- **Swarm Manager**: A single machine within a cluster which executes commands on the others.
- **Stack**: Orchestrator for the service (or a group of services).

**Machines** refers to each instance of a virtual machine. Docker will spin up new containers for our app on each machine. Docker will then manage those containers together as a cluster. The swarm.

### Building a Swarm

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

> NOTE: In Step 3 above we're joining apidemovm2 to apidemovm1 using apidemovm1's ip address! We are telling docker to connect apidemovm2 as a member of the later.

The last thing we need to do is deploy our app to the swarm. While running the following commands, take a few minutes to read all the information and understand what it means.

```
# Always check output from the following command before proceding. No machines should be active, all should still be running.
docker-machine ls

# Review the environment on the vm. Then configure our shell to talk to it.
docker-machine env apidemovm1
eval $(docker-machine env apidemovm1) 

# verify apidemovm1 is active (you will now see a * in its column)
docker-machine ls

# DEPLOY! Note we are using the -c flag to name the config file and naming the stack apidemolab
docker stack deploy -c docker-compose.yml apidemolab
```

You can use the commands you learned in the Services section to control whether the stack is running or not. Try it. This is also a reminder to use cheatsheets whenever you are learning lots of new commands!

You can also view the IP addresses this way.

Visit the IP address of either of your virtual machines on port 4000. For example, yours may look like this `192.168.99.108:4000`.


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

### Additional Reading

- [Why Virtual Machines?](https://www.virtualbox.org/manual/ch01.html#virt-why-useful)
- [Swarm: Key Concepts](https://docs.docker.com/engine/swarm/key-concepts/)

[Continue to the next section ...](part5.md)
