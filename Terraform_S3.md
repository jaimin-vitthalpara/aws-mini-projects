# Project: Hosting a Static Website on AWS S3 using Terraform

## Objective

The aim of this project is to automate the process of hosting a static website on Amazon Web Services (AWS) using the AWS S3 service and infrastructure as code (IaC) with Terraform. The website will be hosted using a simple HTML file that is uploaded from a specified absolute local file path. This project focuses on creating an S3 bucket, configuring it for static website hosting, and uploading the website content to the S3 bucket using Terraform.

## Steps

1. **Create an S3 Bucket:**
   - A new S3 bucket will be created using Terraform. The bucket will be configured to allow static website hosting.
   - The bucket name will be unique and globally accessible as S3 bucket names are globally unique.

2. **Enable Static Website Hosting:**
   - Terraform will configure the bucket to serve static content like HTML, CSS, JavaScript, and image files, allowing it to function as a static website.
   - The index file and error page will be specified for default redirects (e.g., `index.html` as the homepage).

3. **Upload Website Content:**
   - The content of the static website (e.g., HTML, CSS, JS files) will be uploaded to the S3 bucket from the absolute file path specified by the user. Terraform will automate this step, making it easy to update the content whenever required.

4. **Configure Bucket Policy:**
   - Terraform will apply a bucket policy to make the content publicly accessible, ensuring users can view the website by visiting the S3 URL.

5. **Access the Website:**
   - After deployment, the website will be accessible through the S3 bucket's public URL, allowing users to access the static site over the web.

## Key Components

- **Terraform**: Infrastructure as Code tool to define and deploy AWS resources.
- **AWS S3**: Object storage service to host the static website content.
- **AWS Bucket Policy**: Configuration for public access to the static content.

## Outcome

By the end of this project, you will have a fully automated infrastructure using Terraform to host a static website on AWS S3. This approach makes it easier to manage and scale your website with minimal manual intervention.
