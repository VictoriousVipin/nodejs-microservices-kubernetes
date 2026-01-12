# Node.js Microservices on Kubernetes

This project demonstrates a microservices architecture using Node.js, deployed on Kubernetes. It consists of three services: User Service, Task Service, and Notification Service, along with MongoDB for data persistence and RabbitMQ for message queuing.

## Architecture

-   **User Service**: Manages user data (CRUD operations)
-   **Task Service**: Handles task management
-   **Notification Service**: Processes notifications via RabbitMQ
-   **MongoDB**: Database for storing user and task data
-   **RabbitMQ**: Message broker for inter-service communication

## Prerequisites

-   Docker installed and running
-   Kubernetes cluster (Minikube, Docker Desktop, or cloud provider)
-   kubectl configured to access your cluster
-   Docker Hub account (for pushing images)

## Setup

1. Clone this repository:

    ```bash
    git clone <repository-url>
    cd nodejs-microservices-kubernetes
    ```

2. Ensure Docker is running and you're logged into Docker Hub:
    ```bash
    docker login
    ```

## Building Docker Images

Build the Docker images for each service:

```bash
# Build images
docker build -t victoriousvipin/nms_user-service ./user-service
docker build -t victoriousvipin/nms_task-service ./task-service
docker build -t victoriousvipin/nms_notification-service ./notification-service

# Push images to Docker Hub
docker push victoriousvipin/nms_user-service:latest
docker push victoriousvipin/nms_task-service:latest
docker push victoriousvipin/nms_notification-service:latest
```

## Deploying to Kubernetes

Apply the Kubernetes manifests in order:

### 1. Deploy MongoDB

```bash
kubectl apply -f k8s/mongodb-deployment.yml
kubectl apply -f k8s/mongodb-svc.yml
```

### 2. Deploy RabbitMQ

```bash
kubectl apply -f k8s/rabbitmq-configmap.yml
kubectl apply -f k8s/rabbitmq-statefulset.yml
kubectl apply -f k8s/rabbitmq-service.yml
```

### 3. Deploy Services

```bash
kubectl apply -f k8s/user-service-deployment.yml
kubectl apply -f k8s/user-service-svc.yml

kubectl apply -f k8s/task-service-deployment.yml
kubectl apply -f k8s/task-service-svc.yml

kubectl apply -f k8s/notification-service-deployment.yml
kubectl apply -f k8s/notification-service-svc.yml
```

### 4. Deploy Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.1/deploy/static/provider/cloud/deploy.yaml
```

### 5. Deploy Ingress

```bash
kubectl apply -f k8s/ingress.yml
```

## Verification

Check the status of your deployments:

### Check Pods

```bash
kubectl get pods
```

### Check Services

```bash
kubectl get svc
```

### Check Deployments

```bash
kubectl get deployments
```

### Check Ingress

```bash
kubectl get ingress
```

### Check Ingress Controller (in ingress-nginx namespace)

```bash
kubectl get svc -n ingress-nginx
```

## Logs and Debugging

### View logs for a specific pod

```bash
kubectl logs <pod-name>
```

Example:

```bash
kubectl logs user-service-abc123-def456
```

### View logs for all pods in a deployment

```bash
kubectl logs deployment/user-service
```

### Check pod events

```bash
kubectl describe pod <pod-name>
```

### Port forward for local testing

```bash
kubectl port-forward svc/user-service 3001:3001
```

Then test the API:

```bash
curl http://localhost:3001/api/users
```

## API Endpoints

-   **User Service**: `http://localhost/api/users`

    -   GET `/api/users` - Get all users
    -   POST `/api/users` - Create a new user
    -   PUT `/api/users/:id` - Update a user
    -   DELETE `/api/users/:id` - Delete a user

-   **Task Service**: `http://localhost/api/tasks`

    -   Similar CRUD operations for tasks

-   **Notification Service**: Handles notifications via RabbitMQ

## Scaling

Scale deployments as needed:

```bash
kubectl scale deployment user-service --replicas=5
```

## Troubleshooting

1. **Pods not starting**: Check pod status with `kubectl describe pod <pod-name>`
2. **Services not accessible**: Verify service configuration and pod labels
3. **Ingress not working**: Check ingress controller status and ingress rules
4. **Database connection issues**: Ensure MongoDB pod is running and service is accessible
5. **Image pull errors**: Make sure images are pushed to Docker Hub and accessible

## Cleanup

To remove all resources:

```bash
kubectl delete -f k8s/
kubectl delete -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.1/deploy/static/provider/cloud/deploy.yaml
```
