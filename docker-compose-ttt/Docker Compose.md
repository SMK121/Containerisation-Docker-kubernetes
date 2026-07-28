# Docker Compose - Tic Tac Toe Application

## Overview

This task demonstrates how to deploy a multi-container application using Docker Compose.

The application consists of:

- A Node.js Tic Tac Toe web application
- A MongoDB database

Docker Compose is used to create, configure, and manage both containers together.

Instead of manually creating and connecting containers, Docker Compose allows the whole application stack to be started using one configuration file:

```bash
docker compose up
```

---

# Application Architecture

The deployment contains three services:

```
                Docker Compose

        ┌─────────────────────┐
        │     App Service      │
        │  Node.js TTT App     │
        │ Port: 3000           │
        └──────────┬──────────┘
                   │
                   │ MONGODB_URI
                   │
        ┌──────────▼──────────┐
        │   MongoDB Service    │
        │   Database           │
        │   Port: 27017        │
        └──────────┬──────────┘
                   │
                   │
        ┌──────────▼──────────┐
        │ Persistent Volume    │
        │ mongo-data           │
        └─────────────────────┘


        Seed Service
        Runs database seed script
        Inserts initial data
```

---

# Docker Images Used

## Node.js Application Image

The application uses the Docker image created previously and pushed to Docker Hub:

```
smk121/tech610-tttapp:1.2.0
```

This image contains:

- Node.js application
- Application dependencies
- Seed script

---

## MongoDB Image

The database uses the official MongoDB image:

```
mongo:8
```

MongoDB provides the database service required by the Tic Tac Toe application.

---

# Docker Compose Services

The `docker-compose.yml` file creates three services:

## 1. App Service

The app service runs the Node.js Tic Tac Toe application.

Example:

```yaml
app:
  image: smk121/tech610-tttapp:1.2.0
```

The container exposes the application on:

```
localhost:3000
```

Port mapping:

```yaml
ports:
  - "3000:3000"
```

The application connects to MongoDB using an environment variable:

```yaml
environment:
  MONGODB_URI: mongodb://mongo:27017/ttt
```

The hostname:

```
mongo
```

refers to the MongoDB service name created by Docker Compose.

---

# 2. MongoDB Service

MongoDB runs as a separate container.

Configuration:

```yaml
mongo:
  image: mongo:8
```

MongoDB uses the default port:

```
27017
```

Port mapping:

```yaml
ports:
  - "27017:27017"
```

---

## MongoDB Persistent Storage

A Docker volume is created:

```yaml
volumes:
  - mongo-data:/data/db
```

This allows MongoDB data to persist even if the container is removed.

Without the volume:

- Removing the container removes the database data.

With the volume:

- Data remains available after restarting containers.

---

# 3. Seed Service

The seed service automatically populates the database with initial data.

Configuration:

```yaml
seed:
  image: smk121/tech610-tttapp:1.2.0
  command: node seeds/seed.js
```

This runs:

```bash
node seeds/seed.js
```

inside a temporary container.

After successful completion, the container exits with:

```
Exited (0)
```

Exit code `0` confirms the seed script completed successfully.

---

# Deployment Process

## Step 1 - Start Docker Compose

From the project directory:

```bash
docker compose up -d
```

The `-d` flag runs containers in detached mode (background).

Docker creates:

- App container
- MongoDB container
- Seed container
- Docker network
- MongoDB volume

---

## Step 2 - Check Running Containers

Command:

```bash
docker ps
```

Example output:

```
docker-compose-ttt-app-1
docker-compose-ttt-mongo-1
```

The app and database containers should both be running.

---

## Step 3 - Check Application Logs

Command:

```bash
docker compose logs app
```

Example:

```
Server running at http://localhost:3000
```

This confirms the Node.js application started successfully.

---

# Database Seeding

Two approaches were tested.

---

# Method 1 - Manual Seeding

The seed script can be executed manually inside the running application container.

Command:

```bash
docker exec -it docker-compose-ttt-app-1 node seeds/seed.js
```

Explanation:

- `docker exec` runs a command inside an existing container.
- `-it` allows interactive execution.
- The seed script inserts initial database records.

Example output:

```
Seeded active app state via /api/seed (10 records).
```

---

# Method 2 - Automatic Seeding

A dedicated seed service was added to the Docker Compose file:

```yaml
seed:
  image: smk121/tech610-tttapp:1.2.0
  command: node seeds/seed.js
```

When running:

```bash
docker compose up
```

Docker automatically:

1. Starts MongoDB
2. Starts the seed container
3. Runs the seed script
4. Inserts database records
5. Stops the seed container

Checking:

```bash
docker ps -a
```

shows:

```
docker-compose-ttt-seed-1   Exited (0)
```

This confirms automatic seeding completed successfully.

---

# Useful Docker Compose Commands

## Start application

```bash
docker compose up -d
```

Starts all services in the background.

---

## Stop application

```bash
docker compose down
```

Stops and removes containers and network.

---

## View containers

```bash
docker ps -a
```

Shows running and stopped containers.

---

## View logs

```bash
docker compose logs
```

Shows logs from all services.

For a specific service:

```bash
docker compose logs app
```

---

## Check Docker volumes

```bash
docker volume ls
```

Shows persistent storage volumes.

Example:

```
docker-compose-ttt_mongo-data
```

---

# Key Concepts Demonstrated

## Docker Compose

Used to define and manage multiple containers as one application.

Benefits:

- Single configuration file
- Easy deployment
- Automatic networking between services

---

## Container Communication

The application communicates with MongoDB using the Compose service name:

```
mongodb://mongo:27017/ttt
```

Docker Compose automatically creates a network allowing containers to communicate.

---

## Persistent Storage

MongoDB data is stored using a Docker volume:

```
mongo-data:/data/db
```

This prevents data loss when containers are recreated.

---

## Microservices Approach

The application follows a simple microservice architecture:

- Application service
- Database service
- Separate responsibilities
- Independent containers

---

# Final Outcome

Completed:

✅ Created Docker Compose configuration  
✅ Used existing Node.js Docker image from Docker Hub  
✅ Added MongoDB database container  
✅ Connected application to database using environment variables  
✅ Added MongoDB persistent volume  
✅ Tested manual database seeding  
✅ Implemented automatic database seeding  
✅ Successfully ran the full application stack using Docker Compose  
