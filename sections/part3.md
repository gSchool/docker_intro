# 3: Share Images with a Registry

So you've created an image for your application. Now you need to upload it somewhere so that your team can use it and deploy it.

For this, we use a **Registry** which, in plain terms, is a cloud service that hosts all of your docker images. It's a server where the team stores your collection repositories. And each repository can contain any number of docker images. 

> NOTE: these images are represented by the tag names you've been creating. So adding a new image to a repository is as simple as giving it a new tag. This is why we can think of tags as different versions of the app. You can imagine a repository might have beta, dev, and production tags for example. 

If you don't already have a Docker Hub account, make one now. The Hub provides a registry service for free.

1. Go to: https://hub.docker.com/ to create an account
2. By default there should be a repository with your username. If not, create one with any name you like.
2. In your terminal, login: `docker login`

Finally, let's make sure our image has been created and then push it to the Registry:
```
# Step 1: Look for api_demo Docker Repository with tag 'latest'
docker image ls
# Step 2: Push! This may take a minute so give it time.
docker push your_repo_name/api_demo:latest
```

You should now be able to see your repository online in the Registry! Visit the registry and find your app. Explore the interface and learn more about what's there. 

From now on, you can pull and run this repository from the CLI instead of locally:

`docker run -p 4000:80 your_repo_name/api_demo:latest`

...and that's the very magic of docker! Sharing is simpler. Your teammates don't need to install or setup anything to run this app. They simply need Docker and then they can run images straight from the repository.

[Continue to the next section ...](sections/part4.md)
