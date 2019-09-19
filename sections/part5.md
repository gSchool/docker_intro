# Part 5: Stacks

You've made it far and you're almost at the finish line!

To recap what you've learned so far:

- Virtual machines are used to run your services (microservices, your application).
- Services can be anything: a backend, a client, a database, etc.
- Stacks are a tool for **orchestrating** groups of services.
- Stacks can contain many services, which are described in the `docker-compose.yml`
- Swarms are a cluster of services, lead by a single manager.
- Docker compose helps us manage service stacks and configure swarms.
- Docker machine helps us manage virtual machines.

As the Docker documentation states: 'A single stack is capable of defining and coordinating the functionality of an entire application (though very complex applications may want to use multiple stacks).'

This is a very high level tool for orchestrating your services and scaling them. Your application may use multiple stacks.

So let's get hands-on to see what this means in practice.

Open your `docker-compose.yml`. We're going to add a new service to our stack. 

1. Copy the new Visualizer service section from the snippet below.
2. Paste it into your file.

Here's what your final result should look like:

```yaml
version: "3"
services:
  web:
    # replace registry/repo:tag with your registry and repo details
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
  # NEW VISUALIZER SERVICE START (no need to edit anything below; this image has been created by Docker and anyone can use it!)
  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8080:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]
    networks:
      - webnet
  # NEW VISUALIZER SERVICE END
networks:
  webnet:
```

Carefully examine the changes.

Using Docker Compose, this is all it takes to add a new service to our stack. Now re-run your docker deploy command: 

`docker stack deploy -c docker-compose.yml apidemolab` 

You should see a message that the stack is being updated. And that's it, your work is done. You've deployed a whole new service to the stack!

**Go to your browser where your service is running (by it's IP address), but on port 8080.** You should see the visualizer representing each machine in the cluster.

![Imgur](https://imgur.com/fIyAfrx.png)

The service we used in this example was created by Docker. The image already exists online in Docker's registry, so we're just using it here as intended. This illustrates the key feature of Docker: easily shared services that are easy to deploy.

We still don't have our counter operating via Redis, however. So next we're going to add a Redis service to persist our visitor data in 3 steps. Follow along carefully.

**Step 1**
1. Copy the new Redis service section from below.
2. Paste it into your compose file.

Your final result should look like this:

```yaml
version: "3"
services:
  web:
    # replace registry/repo:tag with your registry and repo details
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
  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8080:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]
    networks:
      - webnet
  redis:
    image: redis
    ports:
      - "6379:6379"
    volumes:
      - "/home/docker/data:/data"
    deploy:
      placement:
        constraints: [node.role == manager]
    command: redis-server --appendonly yes
    networks:
      - webnet
  # NEW REDIS SERVICE STARTS HERE (no need to edit anything below; this image has been created by Docker and anyone can use it!)
  redis:
    image: redis
    ports:
      - "6379:6379"
    volumes:
      - "/home/docker/data:/data"
    deploy:
      placement:
        constraints: [node.role == manager]
    command: redis-server --appendonly yes
    networks:
  # NEW REDIS SERVICE END
networks:
  webnet:
```

Carefully examine the changes.

**Step 2**
Redis is our data service. It will need a `/data` directory to store information within. We can create that directory on our manager node easily:

`docker-machine ssh apidemovm1 "mkdir ./data"`

**Step 3**
Now that we've added a new service to the stack, update it: 

`docker stack deploy -c docker-compose.yml apidemolab`

You can view the changes in your browser. **It may take a minute for all the services to update and show their current status so be patient.**

Verify that things are running: `docker service ls`

You should have browser tabs open so that you can see both the Visualizer (port 8080) and your apidemolab service (port 4000). Your counter should now be working! Your Visualizer should also now display the new redis service.


### Additional Reading

- [What is container orchestration?](https://blog.newrelic.com/engineering/container-orchestration-explained/)
- [Are there other orchestration tools?](https://www.linux.com/news/8-open-source-container-orchestration-tools-know/)

[Continue to the next section ...](final.md)
