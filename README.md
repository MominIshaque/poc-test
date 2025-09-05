# MyApp Deployment on Kubernetes with Jenkins CI/CD

## 📌 Project Overview
This project demonstrates a complete CI/CD pipeline using **Jenkins**, **Docker**, **Amazon ECR**, and **Kubernetes (EKS)**.  
The pipeline automatically builds a Docker image from the source code, pushes it to Amazon ECR, and deploys it to a Kubernetes cluster.

---

## 📂 Project Structure
```
.
├── Dockerfile        # Defines how to build the application image
├── index.html        # Simple application (static page)
├── Jenkinsfile       # Jenkins pipeline for CI/CD
└── k8s/
    └── deployment.yml  # Kubernetes Deployment & Service manifest
```

---

## 🚀 Workflow
1. **GitHub Push** → Webhook triggers Jenkins pipeline.
2. **Jenkins Pipeline**:
   - Build Docker image from `Dockerfile`.
   - Tag image with build number and push to Amazon ECR.
   - Update Kubernetes manifest (`deployment.yml`) with the new image.
   - Deploy to Kubernetes using `kubectl`.
3. **Kubernetes**:
   - Creates/updates Deployment (`myapp-deployment`).
   - Exposes app via a `LoadBalancer` Service.
   - App becomes accessible via the external load balancer URL.

---

## ⚙️ Prerequisites
- **AWS ECR** repository created.
- **Amazon EKS** cluster configured.
- Jenkins server with:
  - `docker`
  - `awscli`
  - `kubectl`
- IAM permissions for **ECR** and **EKS**.
- GitHub webhook configured to trigger Jenkins.

---

## 🔧 Deployment
### 1. Build & Push Docker Image (automated by Jenkins)
```bash
docker build -t <AWS_ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com/myapp:<TAG> .
docker push <AWS_ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com/myapp:<TAG>
```

### 2. Apply Kubernetes Manifests
```bash
kubectl apply -f k8s/deployment.yml
```

### 3. Verify Deployment
```bash
kubectl get pods
kubectl get svc myapp-service
```

Check the external IP of the service to access the app.

---

## 📜 License
This project is licensed under the MIT License.
