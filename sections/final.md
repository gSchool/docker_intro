# Winding Down

That's it, you've learned all the basics of what Docker is, how to use it, and even learned about some additional docker tools like stacks and swarms.

If you want wind your machine down by turning off the services:

```bash
# Tear down the docker stack
docker stack rm apidemolab
```

Optionally, you can also stop or remove the other parts of the service. This may be helpful if you need your computer resources to run other applications.

```bash
# To stop VMS
docker-machine stop

# Remove worker from swarm
docker-machine ssh myvm2 "docker swarm leave"

# Remove manager from swarm 
docker-machine ssh myvm1 "docker swarm leave --force"

# To remove VMs entirely
docker-machine rm myvm1 myvm2
```

# Summary

Now's the time to stand up next to your partners and clap for each other. You've successfully learned how to use Docker! You have:

- Created images
- Created containers
- Created services
- Managed Swarms
- Managed Stacks

![Imgur](https://imgur.com/9orTJCd.png)
