# CI/CD Pipeline with Jenkins for Docker Image Deployment to AWS ECR

## 📌 **Project Overview**
This project sets up a CI/CD pipeline using Jenkins to automate the process of building, tagging, and pushing Docker images to AWS Elastic Container Registry (ECR). The pipeline automates the process of building Docker images from source code, pushing them to AWS ECR, and preparing for containerized deployments. The pipeline leverages both a standard base image and an optimized Alpine image to compare the build and deployment times, demonstrating the advantages of using lightweight images.

---

## ✅ **Project Goals**
✔️ Automate the Docker build and deployment process using Jenkins.  
✔️ Optimize Docker image size and build time using Alpine-based images.  
✔️ Secure authentication and deployment to AWS ECR using IAM permissions.  
✔️ Test and validate the pipeline performance with different base images.

---

## 🛠️ **Project Architecture**
1. **Jenkins** – Manages the CI/CD pipeline.  
2. **Docker** – Used to build the container images.  
3. **AWS ECR** – Stores Docker images.  
4. **IAM Role/Policy** – Grants permissions for Jenkins to authenticate and push to ECR.  

---

## ⚙️ **Required Jenkins Plugin Installation**
✔️ Git Plugin  
✔️ Docker Pipeline Plugin  
✔️ AWS Credentials Plugin 

---


## 🚀 **Steps to Implement**

### 1. **Setup AWS ECR**
1. Create a new repository in AWS ECR:
   - AWS Console → Elastic Container Registry → Create Repository  
2. Note down the repository URI:  
   `1234567890.dkr.ecr.us-east-1.amazonaws.com/portfolio-website-repo`

---

### 2. **Create IAM Permissions**
Create an IAM policy with the following permissions:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ecr:GetAuthorizationToken",
                "ecr:BatchCheckLayerAvailability",
                "ecr:GetDownloadUrlForLayer",
                "ecr:BatchGetImage",
                "ecr:PutImage",
                "ecr:InitiateLayerUpload",
                "ecr:UploadLayerPart",
                "ecr:CompleteLayerUpload"
            ],
            "Resource": "*"
        }
    ]
}
```

 
✅ Create a new Key like "Demo-ECR" & Attach the policy to the IAM user used for deployment.

---

### 3. **Configure Jenkins Pipeline**
Create a new pipeline in Jenkins with the following script:

**Jenkinsfile**:
```groovy
pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID = "1234567890"
        AWS_REGION = "us-east-1"
        REPO_URL = "1234567890.dkr.ecr.us-east-1.amazonaws.com/portfolio-website-repo"
        IMAGE_TAG = "latest"
    }
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image using the Dockerfile in your local folder
                    dockerImage = docker.build("my-portfolio-img:${IMAGE_TAG}")
                }
            }
        }
        stage('Login to AWS ECR') {
            steps {
                script {
                    // Authenticate Docker to the AWS ECR
                    sh '''
                    aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${REPO_URL}
                    '''
                }
            }
        }
        stage('Tag and Push Docker Image to ECR') {
            steps {
                script {
                    // Tag the image for ECR
                    dockerImage.tag("${REPO_URL}:${IMAGE_TAG}")
                    
                    // Push the Docker image to ECR
                    dockerImage.push("${REPO_URL}:${IMAGE_TAG}")
                }
            }
        }
    }
    post {
        success {
            echo 'Docker Image has been successfully pushed to AWS ECR!'
        }
        failure {
            echo 'The pipeline failed.'
        }
    }
}

```

✅ Make sure AWS CLI is installed on the Jenkins instance.

---

### 4. **Optimize Docker Image Using Alpine**
Instead of using a large base image, switch to a smaller Alpine-based image:

**Dockerfile**:
```dockerfile
# Use an official Nginx image to serve your site
FROM nginx:alpine

# Copy the website files to the Nginx web root directory
COPY . /usr/share/nginx/html

# Expose port 80 so the container can be accessed from a browser
EXPOSE 80

```

✅ **Why Alpine?**  
- Alpine reduces the image size significantly.  
- Faster build and deployment times.  

| Base Image | Size | Build Time | Deployment Time |
|-----------|------|------------|-----------------|
| Nginx (Base) | ~23 MB | 15s | 8s |
| Nginx (Alpine) | ~6 MB | 7s | 3s |

---

## 📊 **Pipeline Execution Results**
Here’s the Jenkins pipeline execution result:

![Jenkins Pipeline Result](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/c69432eda5128f27e2f2c4e5c49481d8c1b4293c/ECR_BASE%26APLINE_pipeline-View.png)

### ✅ **Performance Summary:**
| Stage | Average Time (Base) | Average Time (Alpine) |
|-------|---------------------|-----------------------|
| Checkout SCM | 5s | 3s |
| Build Docker Image | 2m 36s | 33s |
| Login to AWS ECR | 31s | 18s |
| Tag Docker Image | 5s | 4s |
| Push Docker Image to ECR | 43s | 17s |
| Post Actions | 882ms | 755ms |
| **Total Time** | ~4m 24s | ~1m 15s |

✅ **Insights:**  
- The Alpine image reduced the build time from **2m 36s** to **33s**.  
- The total deployment time dropped from **4m 24s** to **1m 15s**.  
- Using Alpine significantly optimized the pipeline performance.  

---

### ✅ **Outcome**
- Successfully built and pushed Docker images to AWS ECR.  
- Optimized image size and reduced build time using Alpine.  
- Fully automated CI/CD pipeline using Jenkins.  


---

### 📸 **Screenshots**

1. **Create ECR REPO**
![Create ECR REPO](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/9c290e08b6314222464682b10a966557cfe3478d/ECR-REPO.png)

2. **Create IAM User & Attach Policies**
![Create IAM User & Attach Policies](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/c54bbf124e86bf45fc5c493b6281d77712fbd573/ECR_IAM_Policy.png)

3. **Successfully build pipeline in Jenkins**
![Jenkins Pipeline Result](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/c54bbf124e86bf45fc5c493b6281d77712fbd573/ECR_BASE-Pipeline-View.png)
![Jenkins Pipeline Result](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/c54bbf124e86bf45fc5c493b6281d77712fbd573/ECR_BASE%26APLINE_pipeline-View.png)

4. **Docker Desktop**
![Docker Desktop](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/c54bbf124e86bf45fc5c493b6281d77712fbd573/Screenshot%20(21).png)

5. **Image Successfully uploaded in ECR REPO (BASE IMG)**
![Image Successfully uploaded in ECR REPO BASE IMG](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/c54bbf124e86bf45fc5c493b6281d77712fbd573/ECR_BASE-IMG-REPO.png)

6. **Image Successfully uploaded in ECR REPO (ALPINE IMG)**
![Image Successfully uploaded in ECR REPO ALPINE IMG](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/c54bbf124e86bf45fc5c493b6281d77712fbd573/ECR_ALPINE-IMG-REPO.png)

