# Custom Nginx Docker Image - Tech 610 Docker Simulation

## Overview

This project demonstrates how to automate the customisation of the default Nginx webpage using a Dockerfile.

Instead of manually entering a running Nginx container and editing the default `index.html` file, a Dockerfile is used to automatically build a custom Nginx Docker image containing a personalised webpage.

The final image was pushed to Docker Hub and can be run by anyone with Docker installed.

---

# Project Structure

The project contains the following files:

```
tech610-mod-nginx-dockerfile
│
├── Dockerfile
├── index.html
└── README.md
```

---

# Creating the Custom HTML Page

A custom `index.html` file was created to replace the default Nginx landing page.

The webpage was customised with:

- Matrix-style black background
- Green terminal-style text
- Tech 610 Docker containerisation information

Example:

```html
<h1>Welcome to the Matrix</h1>

<p>
The page was automatically deployed by creating a custom Docker image using a Dockerfile and running it as a container.
</p>
```

The custom HTML file replaces the default Nginx webpage.

---

# Dockerfile

The Dockerfile was created to automate the Nginx image customisation.

```Dockerfile
FROM nginx:latest

COPY index.html /usr/share/nginx/html/index.html
```

---

# Dockerfile Explanation

## FROM nginx:latest

```Dockerfile
FROM nginx:latest
```

Uses the official Nginx image from Docker Hub as the base image.

This provides:

- Nginx web server
- Required Linux environment
- Default Nginx configuration

Instead of installing and configuring Nginx manually, an existing Nginx image is used.

---

## COPY index.html /usr/share/nginx/html/index.html

```Dockerfile
COPY index.html /usr/share/nginx/html/index.html
```

Copies the custom webpage into the default Nginx web directory.

Nginx serves webpages from:

```
/usr/share/nginx/html
```

The default Nginx `index.html` file is replaced with the custom Tech 610 Matrix webpage.

---

# Building the Docker Image

The custom Docker image was built using:

```bash
docker build -t smk121/nginx-auto:v1 .
```

## Command Explanation

- `docker build`  
  Creates a new Docker image using instructions from the Dockerfile.

- `-t`  
  Assigns a name and tag to the image.

- `smk121/nginx-auto:v1`  
  The image name and version tag.

Breaking down the image name:

```
smk121
|
Docker Hub username

nginx-auto
|
Image name

v1
|
Version tag
```

- `.`  
  Uses the current folder as the build context, allowing Docker to access the Dockerfile and index.html.

---

# Checking the Docker Image

The created image was verified using:

```bash
docker images
```

Example:

```
REPOSITORY             TAG
smk121/nginx-auto      v1
```

This confirms the custom Nginx image was successfully created.

---

# Running the Container

The custom Nginx container was started using:

```bash
docker run -d -p 90:80 smk121/nginx-auto:v1
```

## Command Explanation

### -d

Runs the container in detached mode.

The container runs in the background while allowing the terminal to continue being used.

---

### -p 90:80

Maps a local machine port to the container port.

Format:

```
HOST_PORT:CONTAINER_PORT
```

Example:

```
90:80
```

Means:

```
Computer Port 90
        |
        ↓
Docker Container Port 80
        |
        ↓
Nginx Web Server
```

Nginx listens on port 80 inside the container, while port 90 was used on the local machine to access the webpage.

The webpage can be accessed through:

```
http://localhost:90
```

---

# Pushing the Image to Docker Hub

Docker Hub login:

```bash
docker login
```

The image was tagged using the Docker Hub username:

```bash
docker tag smk121/nginx-auto:v1 smk121/nginx-auto:v1
```

The image was pushed using:

```bash
docker push smk121/nginx-auto:v1
```

The custom Docker image is now stored on Docker Hub and can be accessed by other users.

---

# Running the Image From Docker Hub

Anyone with Docker installed can run the image using:

```bash
docker run -d -p 90:80 smk121/nginx-auto:v1
```

Docker will:

1. Pull the image from Docker Hub
2. Create a Docker container
3. Start the Nginx web server
4. Display the custom Matrix webpage

---

# Manual vs Automated Approach

## Manual Method

The original method involved entering a running Nginx container and manually editing the webpage.

Example:

```bash
docker exec -it container_name bash
```

Then editing:

```
/usr/share/nginx/html/index.html
```

Problems with this approach:

- Changes are manual
- Difficult to repeat
- Not easily shareable
- Changes can be lost when the container is removed

---

# Dockerfile Method

The Dockerfile approach:

- Automates the Nginx configuration
- Creates a repeatable image build process
- Allows others to recreate the same environment
- Makes the application portable
- Supports future CI/CD workflows

---

# Verification

The Docker Hub image was tested by running:

```bash
docker run -d -p 90:80 smk121/nginx-auto:v1
```

The webpage was successfully displayed:

```
Welcome to the Matrix
```

This confirmed:

- Docker image built successfully
- Docker Hub push worked
- Container started correctly
- Nginx served the custom webpage

---

# Final Docker Command

The final command to run the custom image:

```bash
docker run -d -p 90:80 smk121/nginx-auto:v1
```

This command runs the custom Tech 610 Nginx Docker container from Docker Hub.

---

# Custom Nginx Container Running

https://github.com/user-attachments/assets/e386dcea-8333-4862-9769-5ce528da818f


