# Create an image with Dockerfile

## Common Dockerfile Commands

1. **FROM**  
   Specifies the base image to use. Every Dockerfile starts with this instruction.
   
   ```dockerfile
   FROM ubuntu:latest
   ```

2. **RUN**  
   Executes a command in the shell during the image build process. This is often used to install packages.
   
   ```dockerfile
   RUN apt-get update && apt-get install -y python3
   ```

3. **COPY**  
   Copies files or directories from the host filesystem into the image.
   
   ```dockerfile
   COPY . /app
   ```

4. **ADD**  
   Similar to `COPY`, but can also extract compressed files and supports URLs.
   
   ```dockerfile
   ADD myapp.tar.gz /app
   ```

5. **CMD**  
   Specifies the default command to run when the container starts. This can be overridden by providing a command when running the container.
   
   ```dockerfile
   CMD ["python3", "app.py"]
   ```

6. **ENTRYPOINT**  
   Configures a container that will run as an executable. It can be used in conjunction with `CMD` to provide default arguments.
   
   ```dockerfile
   ENTRYPOINT ["python3", "app.py"]
   ```

7. **ENV**  
   Sets environment variables in the image.
   
   ```dockerfile
   ENV APP_ENV=production
   ```

8. **EXPOSE**  
   Documents which ports the container will listen on at runtime.
   
   ```dockerfile
   EXPOSE 80
   ```

9. **VOLUME**  
   Creates a mount point with the specified path and marks it as holding externally mounted volumes from native host or other containers.
   
   ```dockerfile
   VOLUME ["/data"]
   ```

10. **WORKDIR**  
    Sets the working directory for any `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, and `ADD` instructions that follow it in the Dockerfile.
    
    ```dockerfile
    WORKDIR /app
    ```

### Command to Build the Image

To build a Docker image from a Dockerfile, you use the `docker build` command. The syntax is as follows:

```bash
docker build -t myimage:tag .
```

- `-t myimage:tag` specifies the name (`myimage`) and optional tag (`tag`) for the image.
- `.` indicates the current directory where the Dockerfile is located.

### Example Dockerfile

Here's a simple example Dockerfile using some of the commands described above:

```dockerfile
# Use an official Python runtime as a parent image
FROM python:3.9-slim

# Set the working directory in the container
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . .

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

You can build this image by running:

```bash
docker build -t my-python-app .
```