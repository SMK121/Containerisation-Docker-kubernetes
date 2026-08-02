## Deployment Steps

### Part 1 – Deploy the NodeJS Application

Deploy the application:

```bash
kubectl apply -f nodejs-deployment.yml
```

Create the NodePort service:

```bash
kubectl apply -f nodejs-service.yml
```

Verify the deployment:

```bash
kubectl get pods
kubectl get services
```

Open:

```
http://localhost:30000
```

---

### Part 2 – Deploy MongoDB

Deploy the database first:

```bash
kubectl apply -f mongo-deployment.yml
```

Create the MongoDB service:

```bash
kubectl apply -f mongo-service.yml
```

Deploy the updated NodeJS application:

```bash
kubectl apply -f nodejs-deployment.yml
```

Deploy the NodePort service:

```bash
kubectl apply -f nodejs-service.yml
```

Verify all resources:

```bash
kubectl get all
```

## YAML Files

### nodejs-deployment.yml

- Creates the NodeJS Deployment
- Pulls the Docker image from Docker Hub
- Creates 3 application replicas
- Configures the application to communicate with MongoDB using `DB_HOST`

### nodejs-service.yml

- Creates a NodePort Service
- Exposes the application on `localhost:30000`
- Routes traffic to the NodeJS Pods

### mongo-deployment.yml

- Deploys a MongoDB container
- Uses the official `mongo:8` image
- Creates one MongoDB replica

### mongo-service.yml

- Creates an internal ClusterIP Service
- Gives MongoDB the DNS name `mongo-service`
- Allows the NodeJS application to communicate with MongoDB
