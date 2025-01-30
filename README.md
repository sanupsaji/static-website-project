## **📝 Project Overview**
We will create a **basic static website** with **HTML, CSS, and JavaScript**, containerize it using **Docker**, deploy it to **Kubernetes (EKS)** on **AWS**, and set up a **CI/CD pipeline using Jenkins**.

---

## **📂 Project Structure**
```
static-website-project/
│── src/
│   ├── index.html
│   ├── styles.css
│   ├── script.js
│── Dockerfile
│── kubernetes/
│   ├── deployment.yaml
│   ├── service.yaml
│── Jenkinsfile
│── README.md
```

---

# **🚀 Step-by-Step Deployment Guide**
### **1️⃣ Step 1: Set Up the Code Repository**
1. **Create a GitHub repository** called `static-website-project`.
2. Clone it to your local machine:
   ```bash
   git clone https://github.com/your-username/static-website-project.git
   cd static-website-project
   ```
3. Inside the `src/` folder, create `index.html`:

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Static Website</title>
       <link rel="stylesheet" href="styles.css">
   </head>
   <body>
       <h1>Welcome to My Static Website!</h1>
       <p>This is a simple static page deployed using DevOps best practices.</p>
   </body>
   </html>
   ```

4. Create `styles.css`:
   ```css
   body {
       font-family: Arial, sans-serif;
       text-align: center;
       margin: 50px;
       background-color: #f4f4f4;
   }
   h1 {
       color: #333;
   }
   ```

5. Create `script.js` (optional):
   ```js
   console.log("Welcome to the Static Website!");
   ```

---

### **2️⃣ Step 2: Create a Docker Image**
1. Inside the root directory, create a **Dockerfile**:
   ```dockerfile
   FROM nginx:latest
   COPY src/ /usr/share/nginx/html
   EXPOSE 80
   CMD ["nginx", "-g", "daemon off;"]
   ```
2. Build and run locally:
   ```bash
   docker build -t static-website .
   docker run -p 8080:80 static-website
   ```
3. Check `http://localhost:8080` in your browser.

---

### **3️⃣ Step 3: Push Docker Image to AWS ECR**
1. Log in to **AWS Console** and create an **Elastic Container Registry (ECR)** repository.
2. Authenticate Docker with AWS:
   ```bash
   aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com
   ```
3. Tag and push the image:
   ```bash
   docker tag static-website:latest <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/static-website
   docker push <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/static-website
   ```

---

### **4️⃣ Step 4: Create Kubernetes Deployment (EKS)**
1. Install **kubectl** and **AWS CLI**:
   ```bash
   sudo apt install awscli kubectl -y
   ```
2. Create a Kubernetes cluster using **AWS EKS**:
   ```bash
   eksctl create cluster --name my-static-site --region us-east-1 --nodegroup-name my-nodes
   ```
3. Deploy the website using Kubernetes manifests:

   **`deployment.yaml`**
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: static-website
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: static-website
     template:
       metadata:
         labels:
           app: static-website
       spec:
         containers:
         - name: static-website
           image: <your-aws-account-id>.dkr.ecr.us-east-1.amazonaws.com/static-website:latest
           ports:
           - containerPort: 80
   ```

   **`service.yaml`**
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: static-website-service
   spec:
     type: LoadBalancer
     selector:
       app: static-website
     ports:
       - protocol: TCP
         port: 80
         targetPort: 80
   ```

4. Apply the configuration:
   ```bash
   kubectl apply -f kubernetes/deployment.yaml
   kubectl apply -f kubernetes/service.yaml
   ```

5. Get the public IP of the **LoadBalancer**:
   ```bash
   kubectl get svc static-website-service
   ```
   - Open the **EXTERNAL-IP** in a browser.

---

### **5️⃣ Step 5: Set Up CI/CD with Jenkins**
1. Install Jenkins and configure **Git, Docker, AWS CLI, and Kubectl**.
2. Create a **Jenkins Pipeline** and use the following **Jenkinsfile**:

   ```groovy
   pipeline {
       agent any
       environment {
           AWS_ACCOUNT_ID = '<your-aws-account-id>'
           AWS_REGION = 'us-east-1'
           REPO_NAME = 'static-website'
           EKS_CLUSTER = 'my-static-site'
       }
       stages {
           stage('Checkout Code') {
               steps {
                   git 'https://github.com/your-username/static-website-project.git'
               }
           }
           stage('Build Docker Image') {
               steps {
                   sh "docker build -t $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$REPO_NAME:latest ."
               }
           }
           stage('Push Image to AWS ECR') {
               steps {
                   sh """
                   aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                   docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$REPO_NAME:latest
                   """
               }
           }
           stage('Deploy to Kubernetes') {
               steps {
                   sh "kubectl apply -f kubernetes/deployment.yaml"
                   sh "kubectl apply -f kubernetes/service.yaml"
               }
           }
       }
   }
   ```

3. Save and **run the pipeline** in Jenkins.

---

### **6️⃣ Step 6: Monitoring & Logging**
- **Install Prometheus & Grafana for monitoring**:
  ```bash
  helm install prometheus prometheus-community/kube-prometheus-stack
  helm install grafana grafana/grafana
  ```
- **Enable ELK stack for logging**:
  ```bash
  helm install elasticsearch elastic/elasticsearch
  helm install kibana elastic/kibana
  ```

---

# **✅ Final Result**
1. Your **static website** is live on **AWS Kubernetes (EKS)**.
2. The **CI/CD pipeline** automates deployment when code is pushed.
3. Logs and monitoring are available in **Prometheus, Grafana, and ELK Stack**.

Would you like help setting up a **real AWS free-tier environment** for hands-on practice? 🚀# html-page
# static-website-project
