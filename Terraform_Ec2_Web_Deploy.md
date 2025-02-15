# Terraform-Based EC2 Deployment Documentation

## Overview

This document provides step-by-step guidance on deploying an EC2 instance with a hosted website using Terraform. The deployment includes Apache installation, Git integration, and automated provisioning via a user-data script. By following this guide, you will learn how to create and manage cloud infrastructure efficiently using Infrastructure as Code (IaC).

---

## Prerequisites

Before starting the deployment, ensure you have the following:

- **AWS Account**: You need valid AWS credentials with permissions to create EC2 instances.
- **Terraform Installed**: Ensure Terraform is installed on your local machine.
- **GitHub Repository**: A repository containing the website files that will be deployed.

---

## Project Structure

```
project-folder/
│-- main.tf               # Terraform main configuration file
│-- variables.tf          # Input variables definition
│-- terraform.tfvars      # Values for variables
│-- user-data.sh          # Script to set up Apache & deploy the website
```

Each file has a specific role in setting up the infrastructure and automating the deployment process.

---

## Terraform Configuration

### 1. **main.tf** (Core Terraform Code)

This file defines the Terraform provider and resources needed to create an AWS EC2 instance. It specifies the instance type, AMI, security groups, and user-data script for automatic provisioning.

```hcl
provider "aws" {
  region = var.aws_region
}

resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = var.instance_type
  key_name      = var.key_name

  security_groups = ["default"]

  user_data = file("user-data.sh")

  tags = {
    Name = "Terraform-Web-Server"
  }
}

resource "aws_security_group" "Demo-SG" {
  name        = "Demo-SG"
  description = "Allow HTTP & SSH"

  ingress {
    description = "HTTP Port"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "SSH Port"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

---

### 2. **variables.tf** (Defining Variables)

This file defines the input variables used in the Terraform configuration. It makes the configuration dynamic and reusable, allowing values to be changed easily.

```hcl
variable "aws_region" {}
variable "ami_id" {}
variable "instance_type" {}
variable "key_name" {}
```

---

### 3. **terraform.tfvars** (Assigning Values to Variables)

This file assigns actual values to the defined variables, making it easy to manage configurations without modifying the main configuration file.

```hcl
aws_region = "us-east-1"
ami_id = "ami-12345678"
instance_type = "t2.micro"
key_name = "my-key"
```

---

### 4. **user-data.sh** (Provisioning the Instance)

This script is executed automatically when the EC2 instance is launched. It installs Apache, fetches website files from GitHub, and sets up the web server to serve the content.

```bash
#!/bin/bash

# Update the instance
sudo apt update -y

# Install Apache2
sudo apt install apache2 git -y

# Enable Apache to start on boot and start the service
sudo systemctl enable apache2
sudo systemctl start apache2

# Clone website files from GitHub
mkdir -p /tmp/website
git clone YOUR GITHUB REPO LINK /tmp/website/

# Clear existing Apache web directory
sudo rm -rf /var/www/html/*

# Copy the website files to the Apache web directory
sudo cp -r /tmp/website/* /var/www/html/

# Set permissions for the web directory
sudo chown -R www-data:www-data /var/www/html/
sudo chmod -R 755 /var/www/html/

# Restart Apache to apply changes
sudo systemctl restart apache2
```

---

## Deployment Steps

Follow these steps to deploy the infrastructure using Terraform:

1. **Initialize Terraform**

   ```sh
   terraform init
   ```

   This command initializes the working directory and downloads the necessary provider plugins.

2. **Plan the Infrastructure**

   ```sh
   terraform plan
   ```

   This command shows the changes that Terraform will apply to the infrastructure.

3. **Apply the Configuration**

   ```sh
   terraform apply -auto-approve
   ```

   This command deploys the infrastructure as per the configuration.

4. **Access the Website**

   - Retrieve the instance public IP:
     ```sh
     terraform output
     ```
   - Open `http://<PUBLIC_IP>` in a browser to view the hosted website.

---

## Cleanup

If you need to remove the deployed infrastructure, use the following command:

```sh
terraform destroy -auto-approve
```

This will delete all resources created by Terraform.

---

## Conclusion

This setup automates the provisioning of an EC2 instance, installs necessary dependencies, and deploys a website using Terraform and a Bash script. Modify variables as needed for different environments. This approach ensures a repeatable and efficient deployment process for web applications.

---

## Screenshot of Successful Deployment

Below is a screenshot showing the successfully deployed website running on the EC2 instance.

![My Image](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/5f8bf0f368ca733b27b10f8e9f472553e50a6c8f/Ec2_Created.png
)

![My Image](https://github.com/jaimin-vitthalpara/aws-mini-projects/blob/28eddcbbb84a56c138bf81dca427841f77e0f838/13-website.png)
