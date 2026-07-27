# Sparta NodeJS Tic Tac Toe App - Docker Containerisation

## Overview

This project demonstrates how the Sparta NodeJS Tic Tac Toe application was containerised using Docker.

The aim was to package the application code, Node.js runtime, and dependencies into a Docker image, run the application inside a Docker container, test it locally through a browser, and push the completed image to DockerHub.

---

# Task Aim

The objectives of this task were:

- Create a Docker environment for the Sparta NodeJS application
- Create a Dockerfile to automate image creation
- Build a custom Docker image
- Run the application inside a Docker container
- Access the application through a web browser
- Push the Docker image to DockerHub

---

# Project Structure

The project was organised as follows:

```text
docker-run-sparta-app
│
├── Dockerfile
├── README.md
│
└── app
    ├── index.js
    ├── app.js
    ├── server.js
    ├── package.json
    ├── package-lock.json
    ├── seeds/
    ├── public/
    └── other application files
```

The `app` folder contains the Sparta NodeJS application.

The `Dockerfile` contains the instructions required to build the Docker image.

---

# Step 1 - Create Docker Project Folder

A new folder was created to store the Docker configuration and application files.

Folder created:

```text
docker-run-sparta-app
```

The existing Sparta NodeJS application was copied into the `app` folder.

This allowed Docker to access:

- Application source code
- Node package files
- Application resources
- Seed files

---

# Step 2 - Create Dockerfile

A Dockerfile was created to automate the container creation process.

The Dockerfile defines:

- The base image
- Application working directory
- Files copied into the container
- Dependency installation
- Security settings
- Application startup command

## Dockerfile

```dockerfile
FROM node:20

WORKDIR /app

COPY app .

RUN npm ci --omit=dev

USER node

EXPOSE 3000

CMD ["node", "index.js"]
```

---

# Dockerfile Explanation

## FROM node:20

```dockerfile
FROM node:20
```

Uses the official Node.js version 20 image from DockerHub.

### Why:

- Provides the Node.js runtime required by the application
- Avoids manually installing Node.js
- Creates a consistent environment for running the application

---

## WORKDIR /app

```dockerfile
WORKDIR /app
```

Creates and sets `/app` as the working directory inside the container.

### Why:

- Provides a dedicated location for application files
- Ensures Docker commands run from the correct directory
- Keeps the container organised

---

## COPY app .

```dockerfile
COPY app .
```

Copies the Sparta application files into the Docker container.

Files copied include:

- `index.js`
- `package.json`
- `package-lock.json`
- `seeds/`
- `public/`
- Application files

### Why:

The application code must exist inside the container before it can run.

---

## RUN npm ci --omit=dev

```dockerfile
RUN npm ci --omit=dev
```

Installs the required Node.js dependencies.

### Why:

- Uses `package-lock.json` for consistent package versions
- Installs production dependencies only
- Reduces unnecessary packages inside the container

---

## USER node

```dockerfile
USER node
```

Runs the application using the Node user.

### Why:

- Improves container security
- Avoids running the application as root
- Follows Docker security best practices

---

## EXPOSE 3000

```dockerfile
EXPOSE 3000
```

Documents the port used by the application.

### Why:

The Sparta Tic Tac Toe application runs on port `3000`.

---

## CMD

```dockerfile
CMD ["node", "index.js"]
```

Starts the NodeJS application when the container launches.

### Why:

- Automatically starts the application
- Ensures the container runs the correct entry point

---

# Step 3 - Download Node Base Image

Before building the application image, the Node.js base image was downloaded.

## Command

```bash
docker pull node:20
```

## Purpose

Downloads the official Node.js 20 image from DockerHub.

## Why:

The Dockerfile uses:

```dockerfile
FROM node:20
```

Therefore Docker requires this image as the starting layer when creating the application image.

---

# Step 4 - Build Docker Image

The Docker image was created using:

```bash
docker build -t smk121/tech610-tttapp:1.2.0 .
```

## Command Breakdown

| Command | Purpose |
|---|---|
| `docker build` | Creates a Docker image from a Dockerfile |
| `-t` | Gives the image a name and tag |
| `smk121/tech610-tttapp` | DockerHub repository name |
| `1.2.0` | Image version tag |
| `.` | Uses the current directory as the build context |

## Result

The image created:

```text
smk121/tech610-tttapp:1.2.0
```

---

# Step 5 - Docker Build Error and Fix

During the first image build, an error occurred:

```text
Error: Cannot find module '/app/seeds/seed.js'
```

## Cause

The original Dockerfile attempted:

```dockerfile
COPY app/package*.json ./

RUN npm ci --omit=dev
```

The issue was:

- Only `package.json` and `package-lock.json` were copied
- The `seeds` folder was missing
- `npm ci` triggered the postinstall script:

```text
node seeds/seed.js
```

However, the file did not exist inside the container.

---

## Solution

The Dockerfile was changed to:

```dockerfile
COPY app .

RUN npm ci --omit=dev
```

This copied the complete application before installing dependencies.

The container then contained:

```text
/app
├── package.json
├── index.js
└── seeds
    └── seed.js
```

The Docker image then built successfully.

---

# Step 6 - Check Docker Images

Command:

```bash
docker images
```

## Purpose

Displays locally available Docker images.

Example:

```text
REPOSITORY              TAG
smk121/tech610-tttapp   1.2.0
node                    20
```

## Why:

Used to confirm the Docker image was created successfully.

---

# Step 7 - Run Docker Container

Command:

```bash
docker run -d -p 3000:3000 smk121/tech610-tttapp:1.2.0
```

## Purpose

Creates and starts a container from the Docker image.

## Command Breakdown

| Option | Purpose |
|---|---|
| `docker run` | Creates and starts a container |
| `-d` | Runs container in detached/background mode |
| `-p` | Maps ports |
| `3000:3000` | Maps local port 3000 to container port 3000 |

---

# Step 8 - Check Running Container

Command:

```bash
docker ps
```

## Purpose

Shows currently running Docker containers.

Example:

```text
CONTAINER ID   IMAGE
c5c20a46ca69   smk121/tech610-tttapp:1.2.0
```

Confirmed:

- Container is running
- Correct image is being used
- Port mapping is successful

---

# Step 9 - Test Application

The application was tested using:

```
http://localhost:3000
```

Result:

✅ Sparta Tic Tac Toe game loaded successfully in the browser.

---

# Step 10 - Push Docker Image to DockerHub

## Login to DockerHub

Command:

```bash
docker login
```

Purpose:

Authenticates Docker with DockerHub.

---

## Push Image

Command:

```bash
docker push smk121/tech610-tttapp:1.2.0
```

Purpose:

Uploads the Docker image to DockerHub.

## Why:

Allows other users or systems to download and run the container.

---

# Step 11 - Run DockerHub Image

Anyone can run the published Docker image using:

```bash
docker run -d -p 3000:3000 smk121/tech610-tttapp:1.2.0
```

Then access:

```
http://localhost:3000
```

---

# Key Docker Commands Used

| Command | Purpose |
|---|---|
| `docker pull` | Downloads an image from DockerHub |
| `docker build` | Creates a Docker image |
| `docker images` | Lists local Docker images |
| `docker run` | Creates and starts a container |
| `docker ps` | Shows running containers |
| `docker logs` | Displays container logs |
| `docker stop` | Stops a container |
| `docker login` | Logs into DockerHub |
| `docker push` | Uploads image to DockerHub |

---

# Final Outcome

The Sparta NodeJS Tic Tac Toe application was successfully:

✅ Containerised using Docker  
✅ Built into a custom Docker image  
✅ Run inside a Docker container  
✅ Tested through a browser  
✅ Published to DockerHub  

Docker provides a portable environment by packaging:

- Application code
- Dependencies
- Runtime environment

into a single container image.
