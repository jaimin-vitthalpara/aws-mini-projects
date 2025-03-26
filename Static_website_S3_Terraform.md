# Project: Hosting a Static Website on AWS S3 using Terraform

## Objective

The aim of this project is to automate the process of hosting a static website on Amazon Web Services (AWS) using the AWS S3 service and infrastructure as code (IaC) with Terraform. The website will be hosted using a simple HTML file that is uploaded from a specified absolute local file path. This project focuses on creating an S3 bucket, configuring it for static website hosting, and uploading the website content to the S3 bucket using Terraform.

## Steps

1. **Create an S3 Bucket:**
   - A new S3 bucket will be created using Terraform. The bucket will be configured to allow static website hosting.
   - The bucket name will be unique and globally accessible as S3 bucket names are globally unique.

      ```
       resource "aws_s3_bucket" "bucket" {
        bucket  = "photography-portfolio-bucket"
        tags = {
          Name = "Photography Portfolio Website"
        }
      }

2. **Block Public Access settings:**
   - Disble block policy 
  
          ```
           resource "aws_s3_bucket_public_access_block" "public_access_block" {
           bucket = aws_s3_bucket.bucket.id

           block_public_acls       = false
           ignore_public_acls      = false
           block_public_policy     = false
           restrict_public_buckets = false
     }



3. **Enable Static Website Hosting:**
   - Terraform will configure the bucket to serve static content like HTML, CSS, JavaScript, and image files, allowing it to function as a static website.
   - The index file and error page will be specified for default redirects (e.g., `index.html` as the homepage).
  
          ```
           resource "aws_s3_bucket_website_configuration" "website" {
           bucket = aws_s3_bucket.bucket.id

           index_document {
             suffix = "index.html"
           }

           error_document {
             key = "404.html"
           }
      }

     
4. **Upload Website Content:**
   - The content of the static website (e.g., HTML, CSS, JS files) will be uploaded to the S3 bucket from the absolute file path specified by the user.
   - Terraform will automate this step, making it easy to update the content whenever required.
  
        ```
         resource "aws_s3_object" "object" {
           for_each = fileset("ABSOLUTE PATH", "**/*")
           bucket   = aws_s3_bucket.bucket.id
           key = each.key
           source = "ABSOLUTE PATH/${each.key}"

           content_type = lookup(
             {
               "html" = "text/html"
               "css"  = "text/css"
               "js"   = "application/javascript"
               "png"  = "image/png"
               "jpg"  = "image/jpeg"
               "jpeg" = "image/jpeg"
             },
             regex("[^.]*$", each.value),
             "application/octet-stream"
        )
   }


5. **Configure Bucket Policy:**
   - Terraform will apply a bucket policy to make the content publicly accessible, ensuring users can view the website by visiting the S3 URL.
  
     
        ```
         resource "aws_s3_bucket_policy" "portfolio_bucket_policy" {
            bucket = aws_s3_bucket.bucket.id

            policy = jsonencode({
               Version = "2012-10-17",
               Statement = [
                  {      
                    Effect = "Allow"
                    Principal = "*"
                    Action = "s3:GetObject"
                    Resource = "arn:aws:s3:::photography-portfolio-bucket/*"
                 }
               ]
            })
         }


6. **Access the Website:**
   - After deployment, the website will be accessible through the S3 bucket's public URL, allowing users to access the static site over the web.
  
     
        ```
         output "bucket_endpoint" {
           value = aws_s3_bucket_website_configuration.website.website_endpoint
         }

        
7. **Automating Terraform Commands:**
   -  You can automate the Terraform deployment by running the following Terraform commands:
   - After you have configured your AWS credentials using `aws configure`, you can automate the process of running Terraform commands using the following steps:
     
      6.1. **Configure AWS Credentials:**:

      - If you haven't already set your AWS credentials, you can do so by running:

            aws configure
      - This will prompt you to enter your AWS Access Key, AWS Secret Key, and the region (e.g., us-east-1).
        
     
     6.2. **Initialize Terraform:**:

      - This command initializes your Terraform working directory.

            terraform init

     6.3. **Validate the Terraform Configuration:**:

      - This checks for syntax errors in your Terraform configuration files.

            terraform validate

     6.4. **Plan the Terraform Deployment:**:

      - This shows you the changes Terraform will make without actually applying them.

            terraform plan

      6.5. **Apply the Terraform Configuration:**:

      - This applies the changes to your AWS environment.

            terraform apply -auto-approve
 
     
## Key Components

- **Terraform**: Infrastructure as Code tool to define and deploy AWS resources.
- **AWS S3**: Object storage service to host the static website content.
- **AWS Bucket Policy**: Configuration for public access to the static content.

## Outcome

By the end of this project, you will have a fully automated infrastructure using Terraform to host a static website on AWS S3. This approach makes it easier to manage and scale your website with minimal manual intervention.

## Screenshots
1. **Terraform S3 Bucket Code**
![Terraaform Code](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/00566d27048ee73fb31bb436cfcffe89e4f3dbfd/Screenshot%20(22).png)

2. **Terraform S3 Bucket Code Successfully Run**
![Terraform S3 Bucket Code Successfully Run](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/9738053d882a76ba874137f0f6e29a06b1786a78/Screenshot%20(25).png)

3. **S3 Bucket Created**
![S3 Bucket Created](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/9738053d882a76ba874137f0f6e29a06b1786a78/Terraform_S3_Bucket-Created.png)

4. **S3 Bucket Objects**
![S3 Bucket Objects](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/9738053d882a76ba874137f0f6e29a06b1786a78/Terraform_S3_Bucket-Objects.png)

5. **WebSite**
![WebSite](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/9738053d882a76ba874137f0f6e29a06b1786a78/Terraform_S3_Bucket-Host-websites.png)
