# 2: Creating a Docker Image

Now that you've installed Docker have seen how it works, we'll get started by creating our own Docker image.

1. Make sure you've cloned this repository.
2. Add a new file to it named `Dockerfile`
3. Open the project in your code editor and edit the `Dockerfile`.
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

Read the comments of the file to understand what each command is doing. 

**Dockerfile**s are used to define how your application should run. Everyone on your team will use this to run and/or deploy this app as a service.

Finally, build and run the app: 

```bash
# Step 1: Build. Think of the tag as the build version for the Docker Image
docker build -t apidemo:latest .

# Step 2: Run. We name the Docker Container instance apidemo and run it here. 
docker run -p 4000:80 apidemo

# OR run as a detached container instead, which does the same thing. Try both!
docker run -d -p 4000:80 apidemo

# Step 3: Open your browser to localhost:4000 to see your app running.

# Step 4: Stop your container. To stop a detached container, first get the container ID
docker container ls

# ...then use the stop command
docker container stop paste_container_id_here

```
> NOTE: If you don't provide a tag (image name) Docker assumes 'latest'. Make sure you're working from the correct image! Here we are working from apidemo:latest even though we didn't provide 'latest'.

You've built a Docker app. JOY! But you're using Docker for a reason: to share and collaborate. Next we'll talk about how to share your apps.


### Trouble-shooting

Here's some common issues you may run into while learning Docker, along with some ways to troubleshoot.

**Tip 1: Docker connection issues**
If the docker server seems to be down or you're getting errors that say docker isn't running, first try closing your terminal session out and starting a new one. Then run `docker info` or `docker version` to check if it's running.

[Continue to the next section ...](part3.md)
