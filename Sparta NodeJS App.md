# Sparta NodeJS App - Docker Containerisation

## Task Aim

The aim of this task was to containerise the Sparta NodeJS Tic Tac Toe application using Docker.

The objectives were:

- Create a Docker environment for the NodeJS application
- Build a custom Docker image using a Dockerfile
- Run the application inside a Docker container
- Test the application through a browser using port 3000
- Push the Docker image to DockerHub

---

# Step 1 - Create Docker Project Folder

A new folder was created to store the Docker configuration files.

Project location:


docker-run-sparta-app


The folder contains:


docker-run-sparta-app
│
├── Dockerfile
├── README.md
│
└── app
├── index.js
├── package.json
├── package-lock.json
├── seeds
├── public
└── other application files


The `app` folder contains the Sparta NodeJS application files.

The `Dockerfile` contains the instructions required to create the Docker image.

---

# Step 2 - Create Dockerfile

A Dockerfile was created to automate the container creation process.

Dockerfile:

```dockerfile
FROM node:20

WORKDIR /app

COPY app .

RUN npm ci --omit=dev

USER node

EXPOSE 3000

CMD ["node", "index.js"]

```

Dockerfile Explanation
FROM node:20
FROM node:20

Uses the official Node.js version 20 Docker image.

Why:

Provides the Node runtime environment
Allows the application to run inside the container
Avoids manually installing Node.js
WORKDIR /app
WORKDIR /app

Creates and moves into the /app directory inside the container.

Why:

Provides a dedicated location for application files
Makes commands easier to manage
COPY app .
COPY app .

Copies the Sparta application files into the container.

Files copied include:

index.js
package.json
package-lock.json
seeds folder
public folder

Why:

The application code needs to exist inside the container to run.

RUN npm ci --omit=dev
RUN npm ci --omit=dev

Installs the required Node dependencies.

Why:

Installs packages from package-lock.json
Creates a consistent production environment
Avoids installing unnecessary development dependencies
USER node
USER node

Runs the application using the Node user.

Why:

Improves security
Avoids running the application as root
EXPOSE 3000
EXPOSE 3000

Documents that the application listens on port 3000.

Why:

The Sparta application runs on port 3000.

CMD
CMD ["node", "index.js"]

Starts the application when the container starts.

Why:

Automatically launches the NodeJS application.

Step 3 - Download Node Base Image

Before building the application image, the Node image was downloaded.

Command:

docker pull node:20

Purpose:

Downloads the official Node.js 20 image from DockerHub.

Why:

The Dockerfile uses:

FROM node:20

so Docker requires this image as the base layer.

Step 4 - Build Docker Image

Command:

docker build -t smk121/tech610-tttapp:1.2.0 .

Purpose:

Creates a Docker image from the Dockerfile.

Command breakdown:

Command	Purpose
docker build	Creates a new Docker image
-t	Assigns a name and tag
smk121/tech610-tttapp	DockerHub repository name
1.2.0	Image version number
.	Uses current folder as build context

Result:

smk121/tech610-tttapp:1.2.0
Step 5 - Troubleshooting Docker Build Error
Initial Error

During the first build attempt:

Error: Cannot find module '/app/seeds/seed.js'
Cause

The first Dockerfile attempted:

COPY app/package*.json ./

RUN npm ci --omit=dev

The problem was:

Only package files existed inside the container
The seeds folder had not been copied yet
npm ran the postinstall script:
node seeds/seed.js

but the file did not exist.

Solution

The Dockerfile was changed to:

COPY app .

RUN npm ci --omit=dev

Now the container contains:

/app
├── package.json
├── index.js
└── seeds
    └── seed.js

before npm install runs.

The build then completed successfully.

Step 6 - Check Docker Images

Command:

docker images

Purpose:

Displays locally available Docker images.

Example:

REPOSITORY                  TAG
smk121/tech610-tttapp       1.2.0
node                        20

Why:

Used to confirm that the image was created successfully.

Step 7 - Run Docker Container

Command:

docker run -d -p 3000:3000 smk121/tech610-tttapp:1.2.0

Purpose:

Creates and starts a container from the Docker image.

Command breakdown:

Option	Purpose
docker run	Creates a container
-d	Runs container in background
-p	Maps ports
3000:3000	Maps local port 3000 to container port 3000
Step 8 - Check Running Container

Command:

docker ps

Purpose:

Shows currently running containers.

Example:

CONTAINER ID   IMAGE
c5c20a46ca69   smk121/tech610-tttapp:1.2.0

Confirmed:

Container is running
Application port is exposed
Image is being used correctly
Step 9 - Test Application

The application was accessed through:

http://localhost:3000

Result:

✅ Sparta Tic Tac Toe game loaded successfully.

Step 10 - Push Docker Image to DockerHub

Login:

docker login

Purpose:

Authenticates Docker with DockerHub.

Push image:

docker push smk121/tech610-tttapp:1.2.0

Purpose:

Uploads the Docker image to DockerHub.

Why:

Allows other users or systems to download and run the application.

Step 11 - Run DockerHub Image

Anyone can run the published image using:

docker run -d -p 3000:3000 smk121/tech610-tttapp:1.2.0

Then access:

http://localhost:3000

Key Docker Commands Summary
Command	Purpose
docker pull	- Downloads an image from DockerHub
docker build -	Creates a Docker image
docker images -	Lists local images
docker run -	Creates and starts a container
docker ps	- Shows running containers
docker logs -	Displays container output
docker stop -	Stops a running container
docker login - Logs into DockerHub
docker push	- Uploads image to DockerHub


#Final Outcome

The Sparta NodeJS application was successfully:

Containerised using Docker
Built into a custom Docker image
Run inside a Docker container
Tested through a browser
Published to DockerHub

Docker provides a portable environment by packaging:

Application code
Dependencies
Runtime environment

into a single container image.
