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

The difference between **images** and **containers** is that images represent your application package and it's setup and configuration are defined within the Dockerfile. 

Containers are *running instances* of your application. What makes these containers so consistent for you and your team is that Docker handles the environment setup so that all anyone has to do to run the app is run a Docker container.

[Continue to the next section ...](sections/part2.md)
