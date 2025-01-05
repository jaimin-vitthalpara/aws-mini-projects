# Hosting a Static Website on AWS S3 using Terraform

This guide will help you host a static photography portfolio website on AWS S3 using Terraform. Follow the steps to configure AWS, create an S3 bucket, enable static website hosting, and upload your website files.

## Prerequisites
- [Terraform](https://www.terraform.io/downloads) installed
- AWS CLI configured
- AWS IAM User with programmatic access (Access Key & Secret Key)

## Steps

### Step 1: Install Terraform in VS Code
1. Download and install [VS Code](https://code.visualstudio.com/).
2. Install the **Terraform Extension** for VS Code:
   - Open VS Code.
   - Go to the **Extensions** tab (left sidebar).
   - Search for "Terraform" and click **Install**.
3. Download Terraform and follow the installation instructions [here](https://developer.hashicorp.com/terraform/downloads).

### Step 2: Create the `main.tf` File and Configure AWS Provider
1. In your project folder, create a file named `main.tf`.
2. Inside `main.tf`, define the Terraform configuration for the AWS provider. This configuration sets up the region, access key, and secret key for your AWS environment. If you don’t have an access key or secret key, create an IAM user by following [this guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html).
   
   ```hcl
   terraform {
     required_providers {
       aws = {
         source  = "hashicorp/aws"
         version = "~> 5.0"
       }
     }
   }

   provider "aws" {
     region     = "us-east-1"
     access_key = "YOUR_AWS_ACCESS_KEY"
     secret_key = "YOUR_AWS_SECRET_KEY"
   }
```

### **Step 3: Create a New S3 Bucket**

In this step, we will create an S3 bucket to host your photography portfolio website.

1. **Add the S3 Bucket Configuration**:
   In the `main.tf` file, add the following code to create a new S3 bucket for your website:

```hcl
resource "aws_s3_bucket" "bucket" {
  bucket = "photography-portfolio-bucket"  # Replace with a unique bucket name
  tags = {
    Name = "Photography Portfolio Website"  # Tagging the bucket
  }
}

