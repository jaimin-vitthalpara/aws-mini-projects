# Terraform EC2 Website Deployment

This Terraform project automates the deployment of a static website on an EC2 instance hosted on AWS. It provisions an EC2 instance, configures security groups, installs Apache, and clones a GitHub repository to serve the website.

## 🚀 Prerequisites

Before you begin, ensure you have the following:

- **AWS Account**: An AWS account with the necessary IAM permissions.
- **Terraform**: Install Terraform to manage infrastructure as code.
- **AWS CLI**: Ensure the AWS CLI is installed and configured with your credentials.
- **SSH Key Pair**: An SSH key pair to access the EC2 instance. This needs to be created before starting the deployment process.

## 🧑‍💻 File Structure

### `main.tf`

This file contains the main configuration for your Terraform infrastructure. It includes:

- The **AWS provider** configuration for the region.
- The configuration for creating an **EC2 instance** that will run the website.
- The creation of a **security group** that allows inbound HTTP (port 80) and SSH (port 22).

### `variables.tf`

This file defines the input variables used in the Terraform configuration. It includes variables for:

- The **region** to deploy resources to.
- The **AMI ID** to use for the EC2 instance.
- The **instance type** (e.g., `t2.micro`).
- The **SSH key name** for accessing the EC2 instance.

### `terraform.tfvars`

In this file, you specify the values for the variables defined in `variables.tf`. These values control how the infrastructure is deployed, such as the AWS region, AMI ID, instance type, and SSH key name.

### `user-data.sh`

This shell script runs automatically when the EC2 instance starts. It sets up the necessary environment on the EC2 instance, including:

- Updating the instance to ensure it has the latest software.
- Installing **Apache2** and **Git**.
- Cloning a GitHub repository containing the website files.
- Setting the correct permissions and restarting Apache to serve the website.

## 🛠️ Getting Started

1. **Clone the Repository**: Download the repository to your local machine to begin using the Terraform scripts.

2. **Configure Variables**: Edit the `terraform.tfvars` file to set the region, AMI ID, instance type, and SSH key name that you will use for your EC2 instance.

3. **Initialize Terraform**: Initialize the Terraform working directory. This step prepares Terraform to manage the infrastructure.

4. **Plan the Deployment**: Run a Terraform plan to preview what resources will be created and make sure everything looks correct before proceeding.

5. **Apply the Configuration**: Apply the Terraform configuration to provision the resources on AWS. Terraform will create the EC2 instance, security group, and deploy the website as specified.

6. **Access the Website**: Once the EC2 instance is launched and configured, you can access the website by using the EC2 instance’s public IP address.

## 🔧 Resources Created

- **EC2 Instance**: A new EC2 instance running Apache2 and your website.
- **Security Group**: A security group that allows HTTP traffic (port 80) and SSH access (port 22).

## 💡 How `user-data.sh` Works

The `user-data.sh` script is executed during the startup of the EC2 instance and performs the following tasks:

1. Updates the EC2 instance to ensure it has the latest security patches.
2. Installs **Apache2** and **Git** to set up the web server and clone the website files.
3. Configures Apache to serve the cloned website files from GitHub.
4. Sets appropriate file permissions to ensure Apache can serve the files correctly.
5. Restarts Apache to ensure the website is properly loaded.

The script uses a default GitHub repository URL. If you wish to deploy a different website, simply replace the repository URL in the script.

## 🧹 Clean Up

Once you're done with the infrastructure, you can run the `terraform destroy` command to delete all the resources created by Terraform. This will help avoid any unwanted AWS charges.

