1. **Prepare the Base Docker Image**:

Your base Docker image will contain all necessary dependencies but will not contain the model data.

**Dockerfile.base**:
```Dockerfile
# Use huggingface/transformers-gpu as the base image
FROM huggingface/transformers-gpu

# Set working directory
WORKDIR /app

# Install necessary libraries
RUN pip install fastapi[all] uvicorn transformers

# Copy your API script to the Docker container
COPY api.py ./api.py

# Specify the command to run on container start
CMD ["uvicorn", "api.py:app", "--host", "0.0.0.0", "--port", "8000"]
```

2. **Build and Push the Base Image to Docker Hub**:
```bash
# Build the base image
docker build -f Dockerfile.base -t your_dockerhub_username/base_image_name .

# Push to Docker Hub
docker push your_dockerhub_username/base_image_name
```

Make sure you are logged into Docker Hub using `docker login` before pushing.

3. **Prepare an Image for the Model Data**:

Whenever your model data changes, you can create a new image that is based on the base image and only adds the new model data.

**Dockerfile.model**:
```Dockerfile
# Use the base image from Docker Hub
FROM your_dockerhub_username/base_image_name

# Copy your machine learning model to the Docker container
COPY ./data ./data
```

4. **Build and Run the Model Data Image**:
When you need to deploy a new model, you just build and run this image:

```bash
# Build the model image
docker build -f Dockerfile.model -t your_model_image_name .

# Run with GPU access
docker run --gpus all -p 8000:8000 your_model_image_name
```

This approach ensures that you only need to add the model data, which should be a relatively quick process, instead of reinstalling all dependencies and reconfiguring the environment each time the model changes.


Great! Here's how you can accomplish each of your requirements:

1. **Run in Detach Mode**:
To run a Docker container in the background (detached mode), you can use the `-d` or `--detach` option.

```bash
docker run --gpus all -d -p 1234:8000 your_model_image_name
```

2. **Stopping the Container**:
When you run a container in detached mode, the container's ID is printed to the console. You can use this ID to stop the container later. Here's the typical workflow:

```bash
# Start the container (this will print the container ID)
container_id=$(docker run --gpus all -d -p 1234:8000 your_model_image_name)

# Stop the container using the saved container ID
docker stop $container_id
```

Alternatively, if you didn't save the container ID, you can retrieve it using `docker ps`:

```bash
# List running containers
docker ps

# Use the CONTAINER ID from the above command to stop the container
docker stop CONTAINER_ID
```

3. **Map Port 8000 of the Docker Image to Port 1234 of the Instance**:
The `-p` option of the `docker run` command maps ports from the host to the container. The syntax is `-p host_port:container_port`. In your case, to map port 8000 of the container to port 1234 of the host:

```bash
docker run --gpus all -d -p 1234:8000 your_model_image_name
```

Combining all your requirements, the command to run your Docker container would be:

```bash
docker run --gpus all -d -p 1234:8000 your_model_image_name
```

And to stop it:

```bash
# Find the container ID with `docker ps` and then stop it
docker stop CONTAINER_ID
```

This setup allows you to run the container in the background, map the desired ports, and stop the container when you're done.



If you didn't save the container ID when you started the container, you can retrieve it afterwards using the `docker ps` command. Here's how:

1. **List all running containers**:

```bash
docker ps
```

This will display a list of running containers, showing their container IDs, images, command, creation time, status, port mappings, and names. 

2. **Identify the container ID**:

From the output of `docker ps`, locate your container by the image name (`your_model_image_name` in your example) or by the port mapping (`0.0.0.0:1234->8000/tcp` if you mapped to port 1234). The first column of the output will show the container ID.

3. **Stop the container**:

Once you have identified the container ID, you can use it to stop the container:

```bash
docker stop CONTAINER_ID
```

Replace `CONTAINER_ID` with the actual ID from the `docker ps` output.

Additionally, if you have a lot of containers and want to filter the list to make it easier to find yours, you can use `grep`:

```bash
docker ps | grep your_model_image_name
```

This will display only the containers running with the image `your_model_image_name`.


To remove all stopped containers, you can use the combination of `docker ps` to list the containers and `docker rm` to remove them.

Here's the command to remove all stopped containers:

```bash
docker rm $(docker ps -a -f status=exited -q)
```

Here's a breakdown:

- `docker ps -a -f status=exited -q`: Lists the IDs of all stopped containers.
- `docker rm`: Removes the containers.

Additionally, Docker provides a convenient command to clean up resources:

```bash
docker container prune
```

This command will remove all stopped containers and prompt you for confirmation before doing so. It's a more straightforward approach if you're comfortable removing all stopped containers.