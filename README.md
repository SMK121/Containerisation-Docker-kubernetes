# Custom Nginx Docker Image - Tech 610 Docker & Kubernetes Simulation

## Overview

This project demonstrates how to automate the customisation of the default Nginx webpage using a Dockerfile.

Instead of manually entering a running Nginx container and editing the default `index.html` file, a Dockerfile is used to automatically build a custom Nginx image containing a personalised webpage.

The final image is pushed to Docker Hub and can be run by anyone with Docker installed.

---

# Project Structure

The project contains the following files:

```
tech610-mod-nginx-dockerfile
│
├── Dockerfile
├── index.html

```

---

# Creating the Custom HTML Page

A custom `index.html` file was created to replace the default Nginx landing page.

The page was customised with:

- Matrix-style black background
- Green terminal-style text
- Tech 610 Docker & Kubernetes simulation information

Example:

```html
<h1>Welcome to the Matrix</h1>

<p>
This custom Nginx webpage was created as part of the Tech 610 Docker & Kubernetes simulation.
</p>

<p>
The page was deployed automatically using a Dockerfile.
</p>
```

---

# Dockerfile

The Dockerfile was created to automate the Nginx image customisation.

```Dockerfile
FROM nginx:latest

COPY index.html /usr/share/nginx/html/index.html
```

## Dockerfile Explanation

### FROM nginx:latest

Uses the official Nginx image from Docker Hub as the base image.

This provides:

- Nginx web server
- Required Linux environment
- Default Nginx configuration

---

### COPY index.html /usr/share/nginx/html/index.html

Copies the custom webpage into the default Nginx web directory.

The original Nginx page is replaced with the custom Tech 610 Matrix page.

---

# Building the Docker Image

The custom Docker image was built using:

```bash
docker build -t smk121/nginx-auto:v1 .
```

Explanation:

- `docker build` creates a new image
- `-t` assigns a name and tag
- `smk121/nginx-auto:v1` is the image name and version
- `.` uses the current folder as the build context

---

# Checking the Docker Image

The created image was verified using:

```bash
docker images
```

Example output:

```
REPOSITORY             TAG
smk121/nginx-auto      v1
```

---

# Running the Container

The custom Nginx container was started using:

```bash
docker run -d -p 90:80 smk121/nginx-auto:v1
```

Explanation:

- `-d` runs the container in detached mode
- `-p 90:80` maps local port 90 to container port 80
- `smk121/nginx-auto:v1` is the custom Docker image

The webpage can then be accessed through:

```
http://localhost:90
```

---

# Pushing the Image to Docker Hub

Docker Hub login:

```bash
docker login
```

The image was pushed using:

```bash
docker push smk121/nginx-auto:v1
```

The image is now available publicly through Docker Hub.

---

# Running the Image From Docker Hub

Anyone with Docker installed can run the image using:

```bash
docker run -d -p 90:80 smk121/nginx-auto:v1
```

Docker will:

1. Pull the image from Docker Hub
2. Create a container
3. Start the Nginx web server
4. Display the custom webpage

---

# Manual vs Automated Approach

## Manual Method

Previously, the Nginx page could be changed by:

1. Starting an Nginx container
2. Entering the container
3. Editing `/usr/share/nginx/html/index.html`

Example:

```
docker exec -it container_name bash
```

This method is not easily repeatable.

---

## Dockerfile Method

The Dockerfile approach:

- Automates the configuration process
- Creates a repeatable build process
- Allows others to recreate the same environment
- Supports CI/CD workflows

---

# Final Docker Command

The final command to run the custom image:

```bash
docker run -d -p 90:80 smk121/nginx-auto:v1
```

This command will run the custom Tech 610 Nginx Docker container.
