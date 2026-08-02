# Microservices-Task

## Overview
This document provides details on testing various services after running the `docker-compose` file. These services include User, Product, Order, and Gateway Services. Each service has its own endpoints for testing purposes.

---

## Services and Endpoints

### **User Service**
- **Base URL:** `http://localhost:3000`
- **Endpoints:**
  - **List Users:**  
    ```
    curl http://localhost:3000/users
    ```
    Or open in your browser: [http://localhost:3000/users](http://localhost:3000/users)

---

### **Product Service**
- **Base URL:** `http://localhost:3001`
- **Endpoints:**
  - **List Products:**  
    ```
    curl http://localhost:3001/products
    ```
    Or open in your browser: [http://localhost:3001/products](http://localhost:3001/products)

---

### **Order Service**
- **Base URL:** `http://localhost:3002`
- **Endpoints:**
  - **List Orders:**  
    ```
    curl http://localhost:3002/orders
    ```
    Or open in your browser: [http://localhost:3002/orders](http://localhost:3002/orders)

---

### **Gateway Service**
- **Base URL:** `http://localhost:3003/api`
- **Endpoints:**
  - **Users:**  
    ```
    curl http://localhost:3003/api/users
    ```
  - **Products:**  
    ```
    curl http://localhost:3003/api/products
    ```
  - **Orders:**  
    ```
    curl http://localhost:3003/api/orders
    ```

---

## Instructions
1. Start all services using the `docker-compose` file:
   ```
   docker-compose up
   ```
2. Once the services are running, use the above endpoints to verify the functionality.

Happy testing!


# Microservices Kubernetes Deployment Assessment

## Overview

This assignment deploys four Node.js microservices on a local Kubernetes cluster using Minikube.

The application contains the following services:

| Service         | Port | Purpose                                      |
| --------------- | ---: | -------------------------------------------- |
| User Service    | 3000 | Provides user information                    |
| Product Service | 3001 | Provides product information                 |
| Order Service   | 3002 | Creates and retrieves orders                 |
| Gateway Service | 3003 | Routes requests to the backend microservices |

All services are exposed internally using Kubernetes `ClusterIP` Services.

The Gateway Service communicates with the User, Product, and Order services using Kubernetes DNS-based service discovery.

---

## Prerequisites

The following tools are required:

* Docker
* Minikube
* kubectl
* Git
* curl

Verify the installed tools:

```bash
docker --version
minikube version
kubectl version --client
git --version
curl --version
```

Docker must be running before Minikube is started.

Verify Docker:

```bash
docker ps
```
<img width="1477" height="611" alt="image" src="https://github.com/user-attachments/assets/7e96b67f-9907-4356-a069-1a33ccef2eb9" />

---

## 1. Start Minikube

Start Minikube using the Docker driver:

```bash
minikube start \
  --driver=docker \
  --cpus=2 \
  --memory=4096
```

If the system does not have enough memory, use:

```bash
minikube start \
  --driver=docker \
  --cpus=2 \
  --memory=3072
```

Check the Minikube status:

```bash
minikube status
```

Verify the Kubernetes node:

```bash
kubectl get nodes
```
<img width="1417" height="292" alt="image" src="https://github.com/user-attachments/assets/4210d5ce-0e4b-4ecc-8faa-edc87b4d4fee" />


Run the following commands together:

```bash
clear
minikube status
echo
kubectl get nodes
```
---

## 2. Build the Docker Images

The Docker images were built directly inside Minikube so that the Kubernetes cluster could access the images without using an external container registry.

Build the User Service image:

```bash
minikube image build \
  -t user-service:1.0 \
  Microservices/user-service
```

Build the Product Service image:

```bash
minikube image build \
  -t product-service:1.0 \
  Microservices/product-service
```

Build the Order Service image:

```bash
minikube image build \
  -t order-service:1.0 \
  Microservices/order-service
```

Build the Gateway Service image:

```bash
minikube image build \
  -t gateway-service:1.0 \
  Microservices/gateway-service
```

Verify the images:

```bash
minikube image ls | grep service
```

<img width="1225" height="212" alt="image" src="https://github.com/user-attachments/assets/bf48907c-e661-48c0-90fd-2a79431958a4" />

---

## 3. Kubernetes Deployment Configuration

The Assignment contains one Kubernetes Deployment manifest for each microservice:

```text
submission/deployments/
├── user-service.yaml
├── product-service.yaml
├── order-service.yaml
└── gateway-service.yaml
```

Each Deployment includes:

* Correct container image
* Container port
* Environment variables
* CPU requests and limits
* Memory requests and limits
* Liveness probe
* Readiness probe
* Deployment labels
* Pod labels
* Matching Deployment selector
* Container security configuration


## 4. Kubernetes Service Configuration

The project contains one Kubernetes Service manifest for each microservice:

```text
submission/services/
├── user-service.yaml
├── product-service.yaml
├── order-service.yaml
└── gateway-service.yaml
```

All services use the `ClusterIP` type.

This provides internal cluster-level communication and Kubernetes DNS-based service discovery.

The configured service names are:

```text
user-service
product-service
order-service
gateway-service
```

The Gateway Service communicates with the backend services using:

```text
http://user-service:3000
http://product-service:3001
http://order-service:3002
```

---

## 5. Validate the Kubernetes YAML Files

Perform a client-side validation before creating the Kubernetes resources.

Validate the Services:

```bash
kubectl apply \
  --dry-run=client \
  -f submission/services/
```

Validate the Deployments:

```bash
kubectl apply \
  --dry-run=client \
  -f submission/deployments/
```

result:

```text
service/user-service created (dry run)
service/product-service created (dry run)
service/order-service created (dry run)
service/gateway-service created (dry run)

deployment.apps/user-service created (dry run)
deployment.apps/product-service created (dry run)
deployment.apps/order-service created (dry run)
deployment.apps/gateway-service created (dry run)
```
<img width="1496" height="512" alt="image" src="https://github.com/user-attachments/assets/97f6e880-436e-42b9-a6c4-7775ea64e6bf" />

<img width="1092" height="197" alt="image" src="https://github.com/user-attachments/assets/13fc15c8-c020-4bf7-a2ec-7576e932bb37" />

---

## 6. Deploy the Kubernetes Services

Create all four ClusterIP Services:

```bash
kubectl apply -f submission/services/
```

Expected result:

```text
service/gateway-service created
service/order-service created
service/product-service created
service/user-service created
```

---

## 7. Deploy the Applications

Create all four Kubernetes Deployments:

```bash
kubectl apply -f submission/deployments/
```

Expected result:

```text
deployment.apps/gateway-service created
deployment.apps/order-service created
deployment.apps/product-service created
deployment.apps/user-service created
```

### Screenshot Capture Point 3 — Resource Creation

For a clean screenshot, delete and reapply the resources only when necessary.

Normally, run:

```bash
clear
kubectl apply -f submission/services/
kubectl apply -f submission/deployments/
```

The result may show either:

```text
created
```

---

## 8. Verify Deployment Rollout

Check whether each Deployment completed successfully:

```bash
kubectl rollout status deployment/user-service
kubectl rollout status deployment/product-service
kubectl rollout status deployment/order-service
kubectl rollout status deployment/gateway-service
```

result:

```text
deployment "user-service" successfully rolled out
deployment "product-service" successfully rolled out
deployment "order-service" successfully rolled out
deployment "gateway-service" successfully rolled out
```
<img width="1286" height="230" alt="image" src="https://github.com/user-attachments/assets/d85836b1-c236-40c8-9427-60d9a47e816d" />

---

## 9. Verify Running Pods

Check the pod status:

```bash
kubectl get pods
```
<img width="922" height="185" alt="image" src="https://github.com/user-attachments/assets/6454d791-6af8-4df9-8881-ba6458043f7b" />

For more detailed information:

```bash
kubectl get pods -o wide
```

<img width="1565" height="166" alt="image" src="https://github.com/user-attachments/assets/c5063410-8821-45eb-b694-145a5f952d31" />



---

## 10. Verify Kubernetes Services

Check all Services:

```bash
kubectl get services
```

<img width="1017" height="202" alt="image" src="https://github.com/user-attachments/assets/ccebc01d-b4cf-44d7-b862-67292fd6cc8c" />

---

## 11. Verify Service Endpoints

Run:

```bash
kubectl get endpoints
```

Each Service should have a pod IP and port.

<img width="1046" height="220" alt="image" src="https://github.com/user-attachments/assets/9b189285-2f9e-4178-b8d3-9bb5728b1cd8" />

---

## 12. Test Internal Service Communication

A temporary curl pod can be used to test Kubernetes DNS-based service discovery.

Create the test pod:

```bash
kubectl run curl-test \
  --image=curlimages/curl:8.10.1 \
  --restart=Never \
  --command -- sleep 3600
```

Wait until the pod is ready:

```bash
kubectl wait \
  --for=condition=Ready \
  pod/curl-test \
  --timeout=60s
```

### Test User Service

```bash
kubectl exec curl-test -- \
  curl -s http://user-service:3000/health
```

```bash
kubectl exec curl-test -- \
  curl -s http://user-service:3000/users
```

### Test Product Service

```bash
kubectl exec curl-test -- \
  curl -s http://product-service:3001/health
```

```bash
kubectl exec curl-test -- \
  curl -s http://product-service:3001/products
```

### Test Order Service

```bash
kubectl exec curl-test -- \
  curl -s http://order-service:3002/health
```

```bash
kubectl exec curl-test -- \
  curl -s http://order-service:3002/orders
```

### Test Gateway Service

```bash
kubectl exec curl-test -- \
  curl -s http://gateway-service:3003/health
```

```bash
kubectl exec curl-test -- \
  curl -s http://gateway-service:3003/api/users
```

```bash
kubectl exec curl-test -- \
  curl -s http://gateway-service:3003/api/products
```

```bash
kubectl exec curl-test -- \
  curl -s http://gateway-service:3003/api/orders
```

---

## 13. Test the Gateway Using Port Forwarding

Forward local port `3003` to the Gateway Service.

Run this command in terminal 1:

```bash
kubectl port-forward service/gateway-service 3003:3003
```

<img width="1331" height="72" alt="image" src="https://github.com/user-attachments/assets/80543eab-f018-4af0-b0fa-ee8b691ced62" />


```bash
curl -i http://localhost:3003/health
```
<img width="912" height="222" alt="image" src="https://github.com/user-attachments/assets/6a39049d-6be2-4426-bb2e-df22fac6cda0" />

Test users through the Gateway:

```bash
curl -i http://localhost:3003/api/users
```

Test products through the Gateway:

```bash
curl -i http://localhost:3003/api/products
```

Test orders through the Gateway:

```bash
curl -i http://localhost:3003/api/orders
```
<img width="1697" height="890" alt="image" src="https://github.com/user-attachments/assets/a3f3b87a-e0e9-40e1-9567-19a4fed1516e" />


<img width="1626" height="722" alt="image" src="https://github.com/user-attachments/assets/4ab16320-75fb-42f7-9f11-ae50820ea106" />

---

## 14. Test Order Creation

While Gateway port forwarding is active, create an order:

```bash
curl -i \
  -X POST \
  http://localhost:3003/api/orders \
  -H "Content-Type: application/json" \
  -d '{
    "userId": 1,
    "productId": 1
  }'
```

Verify the order:

```bash
curl -s http://localhost:3003/api/orders
```
<img width="1202" height="65" alt="image" src="https://github.com/user-attachments/assets/5066b7d0-6e59-4dd3-bb00-36f8d74da59e" />

The order is stored in the pod memory and may be removed if the Order Service pod restarts.


## 15. Check Application Logs

View User Service logs:

```bash
kubectl logs deployment/user-service
```

View Product Service logs:

```bash
kubectl logs deployment/product-service
```

View Order Service logs:

```bash
kubectl logs deployment/order-service
```

View Gateway Service logs:

```bash
kubectl logs deployment/gateway-service
```

Follow Gateway logs continuously:

```bash
kubectl logs -f deployment/gateway-service
```
<img width="1672" height="727" alt="image" src="https://github.com/user-attachments/assets/4c67fce0-f39e-4e51-958f-eca1b73eb959" />

---

## 16. Verify Resource Requests and Limits

Display the configured resources for all pods:

```bash
kubectl get pods \
  -o custom-columns='POD:.metadata.name,CPU_REQUEST:.spec.containers[*].resources.requests.cpu,MEMORY_REQUEST:.spec.containers[*].resources.requests.memory,CPU_LIMIT:.spec.containers[*].resources.limits.cpu,MEMORY_LIMIT:.spec.containers[*].resources.limits.memory'
```
<img width="1881" height="225" alt="image" src="https://github.com/user-attachments/assets/6c6ff95c-17b1-4162-a8a9-b6d399907467" />

You can also inspect an individual Deployment:

```bash
kubectl describe deployment user-service
```
<img width="1401" height="802" alt="image" src="https://github.com/user-attachments/assets/40948671-c8ac-416d-adb3-9409a1cda745" />

---

## 17. Verify Liveness and Readiness Probes

Run:

```bash
for app in user-service product-service order-service gateway-service
do
  echo "===== $app ====="
  kubectl describe pod -l app=$app |
  grep -A3 -E "Liveness|Readiness"
done
```
<img width="1307" height="716" alt="image" src="https://github.com/user-attachments/assets/8c4fe390-ee40-405b-9fcd-fb5a346ded7e" />

## 18. Check Labels and Selectors

Display pod labels:

```bash
kubectl get pods --show-labels
```
<img width="1725" height="192" alt="image" src="https://github.com/user-attachments/assets/5af5c178-501a-41cb-b5f2-2b07dee0744a" />

Check the User Service selector:


## Troubleshooting

### ImagePullBackOff

Check the affected pod:

```bash
kubectl describe pod <pod-name>
```

Verify that the images exist inside Minikube:

```bash
minikube image ls | grep service
```

Rebuild the missing image:

```bash
minikube image build \
  -t user-service:1.0 \
  Microservices/user-service
```

The Deployment should use:

```yaml
imagePullPolicy: IfNotPresent
```

Restart the Deployment:

```bash
kubectl rollout restart deployment/user-service
```

---

### CrashLoopBackOff

Check the current logs:

```bash
kubectl logs <pod-name>
```

Check the logs from the previous container instance:

```bash
kubectl logs <pod-name> --previous
```

Check pod events:

```bash
kubectl describe pod <pod-name>
```

---

### Service Has No Endpoints

Check the endpoints:

```bash
kubectl get endpoints
```

If a Service displays `<none>`, compare its selector with the pod labels:

```bash
kubectl get pods --show-labels
```

```bash
kubectl get service user-service \
  -o yaml
```

The Service selector must match the pod label.

Example:

```yaml
selector:
  app: user-service
```

---

### Gateway Cannot Contact a Backend Service

Check all Services:

```bash
kubectl get services
```

Check all Service endpoints:

```bash
kubectl get endpoints
```

Test the backend service directly:

```bash
kubectl run curl-test \
  --image=curlimages/curl:8.10.1 \
  --restart=Never \
  --rm -i \
  --command -- \
  curl -s http://user-service:3000/users
```

Check the Gateway logs:

```bash
kubectl logs deployment/gateway-service
```

The Kubernetes Service names must be exactly:

```text
user-service
product-service
order-service
```

---

### Pod Is Running but Not Ready

Check the pod:

```bash
kubectl describe pod -l app=user-service
```

Check its logs:

```bash
kubectl logs deployment/user-service
```

Test the health endpoint:

```bash
kubectl port-forward service/user-service 3000:3000
```

In another terminal:

```bash
curl http://localhost:3000/health
```

---

### Local Port 3003 Is Already in Use

Use a different local port:

```bash
kubectl port-forward service/gateway-service 8080:3003
```

Then test:

```bash
curl http://localhost:8080/api/users
```

---

### Reset the Application

Delete the Deployments:

```bash
kubectl delete -f submission/deployments/
```

Delete the Services:

```bash
kubectl delete -f submission/services/
```

Reapply the Services:

```bash
kubectl apply -f submission/services/
```

Reapply the Deployments:

```bash
kubectl apply -f submission/deployments/
```

---


## Final Validation

Check all Kubernetes resources:

```bash
kubectl get all
```
<img width="1067" height="802" alt="image" src="https://github.com/user-attachments/assets/be186a67-6431-4cbe-997e-89e680bb48bc" />

Check the pods:

```bash
kubectl get pods
```
<img width="972" height="162" alt="image" src="https://github.com/user-attachments/assets/2980a5f3-701d-48fb-bff6-f1efbba1d70a" />

Check the Services:

```bash
kubectl get services
```

Check the endpoints:

```bash
kubectl get endpoints
```

Validate the manifests:

```bash
kubectl apply \
  --dry-run=client \
  -f submission/services/ \
  -f submission/deployments/
```
<img width="1050" height="797" alt="image" src="https://github.com/user-attachments/assets/c67b94a3-faaa-4936-a2d3-f56c696e7bce" />

Verify the submission structure:

```bash
tree submission
```
<img width="932" height="412" alt="image" src="https://github.com/user-attachments/assets/ac544278-f8ff-49b2-9816-8551cbbbb1aa" />

---

## Cleanup

Delete the application Deployments:

```bash
kubectl delete -f submission/deployments/
```

Delete the application Services:

```bash
kubectl delete -f submission/services/
```

Stop Minikube:

```bash
minikube stop
```

To delete the Minikube cluster completely:

```bash
minikube delete
```

Deleting the Minikube cluster also removes the locally stored Minikube images.
<img width="1185" height="471" alt="image" src="https://github.com/user-attachments/assets/ccfcc392-113f-45a9-a88e-26a0cb445dd0" />

