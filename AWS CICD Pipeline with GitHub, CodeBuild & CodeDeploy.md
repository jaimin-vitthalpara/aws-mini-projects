
# AWS CI/CD Pipeline with GitHub, CodeBuild & CodeDeploy


## 📘 Project Overview

This project demonstrates a complete CI/CD pipeline for deploying a static portfolio website using AWS services. The website source code is hosted on GitHub, and AWS CodePipeline is used to automate the build and deployment process. CodeBuild fetches the code, creates a ZIP artifact, stores it in S3, and CodeDeploy deploys the website to an EC2 instance running Ubuntu 22.04.

The goal is to streamline deployments for static websites using fully managed AWS services, making your infrastructure reproducible and scalable.

---

👉 [Static Website CI/CD Project Files & Folders](https://github.com/jaimin-vitthalpara/IT-Company-Portfolio.git)

---
## 🔧 Tools & Services Used

| Service        | Purpose                              |
|----------------|--------------------------------------|
| GitHub         | Code repository                      |
| CodePipeline   | CI/CD orchestration                  |
| CodeBuild      | Build and zip source code            |
| S3             | Store ZIP artifact                   |
| CodeDeploy     | Deploy artifact to EC2 instance      |
| EC2            | Host static website on NGINX         |

---

## 📁 Folder Structure

```
IT-Company-Portfolio/
├── about.html
├── contact.html
├── index.html
├── portfolio.html
├── price.html
├── service.html
├── css/
├── js/
├── img/
├── vendor/
├── scripts/
│   ├── after_install.sh
│   ├── start_server.sh
│   └── validate.sh
├── buildspec.yml
└── appspec.yml
```

---

## 🔹 1. GitHub Setup
- Push your static website along with `buildspec.yml`, `appspec.yml`, and `scripts/` to a public or private GitHub repository.

---

## 🔹 2. EC2 Configuration & CodeDeploy Agent Setup

Launch an EC2 instance (Ubuntu 22.04), and run the following to manually install CodeDeploy agent:

```bash
sudo apt-get update
sudo apt-get install ruby-full ruby-webrick wget -y
cd /tmp
wget https://aws-codedeploy-us-east-1.s3.us-east-1.amazonaws.com/releases/codedeploy-agent_1.3.2-1902_all.deb
mkdir codedeploy-agent_1.3.2-1902_ubuntu22
dpkg-deb -R codedeploy-agent_1.3.2-1902_all.deb codedeploy-agent_1.3.2-1902_ubuntu22
sed 's/Depends:.*/Depends:ruby3.0/' -i ./codedeploy-agent_1.3.2-1902_ubuntu22/DEBIAN/control
dpkg-deb -b codedeploy-agent_1.3.2-1902_ubuntu22/
sudo dpkg -i codedeploy-agent_1.3.2-1902_ubuntu22.deb
sudo service codedeploy-agent status
```

- Ensure EC2 IAM role has `AmazonEC2RoleforAWSCodeDeploy` policy.
- Tag instance appropriately (e.g., `Name=CodeDeployDemo`).

---

## 🔹 3. S3 Bucket for Artifact Storage
- Create an S3 bucket to store CodeBuild artifacts (e.g., `cicd-artifact-demo`).
- Provide necessary permissions to CodePipeline and CodeBuild.

---

## 🔹 4. CodeDeploy Setup
- Create a new **CodeDeploy Application**.
- Deployment Group:
  - Environment: EC2/On-premises
  - Select EC2 tag (e.g., `Name=CodeDeployDemo`)
  - Service Role: `AWSCodeDeployRole`

---

## 🔹 5. CodeBuild Setup

### `buildspec.yml`
```yaml
version: 0.2
phases:
  install:
    commands:
      - echo "Installing updates and NGINX..."
      - apt-get update -y
      - apt-get install nginx zip -y

  pre_build:
    commands:
      - echo "Preparing build directory..."
      - mkdir -p build_output

  build:
    commands:
      - echo "Copying site files to build_output directory..."
      - cp -r about.html contact.html index.html portfolio.html price.html service.html css js img vendor build_output/

  post_build:
    commands:
      - echo "Creating ZIP artifact..."
      - cd build_output
      - zip -r ../website.zip .
      - cd ..
      - echo "Build and ZIP complete."

artifacts:
  files:
    - '**/*'
```

---

## 🔹 6. CodePipeline Setup

- Create a pipeline connected to your GitHub repo.
- Stages:
  - **Source**: GitHub
  - **Build**: CodeBuild
  - **Deploy**: CodeDeploy

---

## 🔹 7. AppSpec & Scripts

### `appspec.yml`
```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/html

hooks:
  AfterInstall:
    - location: scripts/after_install.sh
      timeout: 180
      runas: root

  ApplicationStart:
    - location: scripts/start_server.sh
      timeout: 180
      runas: root

  ValidateService:
    - location: scripts/validate.sh
      timeout: 180
      runas: root
```

> Make sure all your shell scripts in `scripts/` are executable:
```bash
chmod +x scripts/*.sh
```

---

## 🖥 Final Output

Once the pipeline is triggered:
- Website code is fetched from GitHub
- CodeBuild zips the content
- Artifact is uploaded to S3
- CodeDeploy deploys the ZIP to EC2 instance
- Your website is accessible via EC2 public IP

---

## 📸 ScreenShot
1. **Code Build**
![Code Build](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/1_Code_Build.png)
![Code Build](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/1_Code_Build2.png)
![Code_Build_Suceess_phases](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/1_Code_Build_Suceess_phases.png)

2. **S3 Bucket Artifact**
![S3 Bucket Artifact](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/5_S3_bkt_Zip.png)

3. **Code Deploy**
![Code Deploy Application](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/2_Code_deploy_application.png)
![Code Deploy group](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/2_Code_deploy_Deploy-grp.png)
![Code Deploy](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/2_Code_deploy_Instance.png)


4. **IAM Roles**
![IAM Role attach to CodeBuild](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/5_CodeBuild_Roles_Policies.png)
![IAM Role attach to CodeDeploy](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/5_CodeDeploy_Roles_Policies.png)
![IAM Role attach to Ec2CodeDeploy](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/5_Ec2CodeDeploy_Roles_Policies.png)


5. **Code Pipeline**
![Code Pipeline](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/3_Code_pipeline.png)
![Code Pipeline Success](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/3_Code_pipeline_successfull.png)


6. **Instance & OP**
![Instance & OP](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/4_Pipeline_Instance.png)
![Instance & OP](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/0760fe08cc00ef0c6cf06364b2c7a8c19d86385d/4_Website_OP.png)

---

## ✅ Conclusion

This project showcases a hands-on approach to automating deployments for a static website using AWS native services. It's beginner-friendly, cost-effective, and ideal for building real-world DevOps and cloud experience.
